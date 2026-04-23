## ⏱️ RB-003: Defense Pipeline Timeout (>300s)

| Metadata | Value |
|----------|-------|
| **Severity** | 🟠 P1 (High) |
| **Trigger** | `defense_pipeline_duration_seconds{status="timeout"} > 0` |
| **Impact** | Tenant receives `degraded_response`; `timeout_occurred=true` logged |
| **SLA** | Resolve in ≤30 min |
| **Invariant** | `DEFENSE_TIMER`, `RESOURCE_CAP` |

### 🚨 Triage (0–5 min)
```bash
# 1. Check active pipelines
psql -c "SELECT run_id, tenant_id, elapsed_ms, status 
         FROM defense_runs 
         WHERE status='analyzing' AND elapsed_ms > 250000;"

# 2. Verify semaphore state
redis-cli GET defense:semaphore:global
redis-cli GET defense:semaphore:tenant:<UUID>

# 3. Check agent health
curl -s https://agents.internal:8080/health | jq .
# Expected: {"status":"healthy","active_agents":N}
```

### 🛠️ Mitigation (5–15 min)
```bash
# 1. Confirm fallback triggered (auto)
grep "timeout_occurred=true" /var/log/fellow/defense.log | tail -5

# 2. Kill stuck agent pod (if hung)
kubectl delete pod agent-<run-id> -n fellow-gov --grace-period=30

# 3. Clear semaphore (if leaked)
redis-cli DEL defense:semaphore:tenant:<UUID>
redis-cli DEL defense:semaphore:global
```

### ♻️ Recovery (15–30 min)
```bash
# 1. Retry with reduced payload
curl -X POST https://api.fellowgovernance.com/api/v1/defense/start \
  -H "Authorization: Bearer <jwt>" \
  -H "Content-Type: application/json" \
  -d '{
    "case_text": "ANPD notice #2026-041",
    "evidence_files": ["notice.pdf"],
    "kb_dimension_override": 256
  }'

# 2. Verify completion
curl -s https://api.fellowgovernance.com/api/v1/defense/<run_id> | jq .timeout_occurred
# Expected: false

# 3. Monitor metrics
# → Grafana: defense_pipeline_duration_seconds p95 < 270s
# → Alert: clear if no timeouts in last 15 min
```

### ✅ Validation
```bash
# 1. Run synthetic defense test
python scripts/smoke_defense.py --tenant test-tenant --dimension 256
# Expected: "Defense completed in 245s (OK)"

# 2. Verify Forensic ZIP integrity
unzip -t forensic_*.zip && gpg --verify signature.sha256
# Expected: "Good signature" + all files OK

# 3. Post-mortem
# → Document root cause in /docs/incidents/RB-003-<date>.md
# → Consider: increase global semaphore? optimize agent X?
```

> 📌 **Defense Pipeline Rules**  
> • Hard timeout: `asyncio.wait_for(task, timeout=300)`  
> • Per-tenant semaphore: max 1 execution  
> • Global semaphore: max 5 executions  
> • Fallback: `degraded_response` + `timeout_occurred=true` + audit log