## 🔴 RB-001: OPA Degraded/Offline

| Metadata | Value |
|----------|-------|
| **Severity** | 🔴 P0 (Critical) if offline >5s / 🟠 P1 (High) if degraded |
| **Trigger** | `opa_eval_duration_seconds{quantile="0.99"} > 2` OR `opa_health_check_fail` |
| **Impact** | Writes blocked (read-only mode) or fallback to cached decisions |
| **SLA** | Restore Normal mode in ≤15 min |
| **Invariant** | `OPA_FINALITY`, `DEFENSE_TIMER` |

### 🚨 Triage (0–5 min)
```bash
# 1. Check OPA health endpoint
curl -s https://opa.internal:8181/v1/system/health | jq .

# 2. Verify policy bundle version
curl -s https://opa.internal:8181/v1/bundles/fellow-gov | jq '.last_successful_activation, .version'

# 3. Check fallback activation in audit_trails
psql -c "SELECT COUNT(*) FROM audit_trails 
         WHERE degraded_mode=true AND timestamp > NOW() - INTERVAL '10 min';"
```

### 🛠️ Mitigation (5–15 min)
```bash
# 1. If timeout 2s–5s (Degraded mode): System auto-uses Redis cache
redis-cli GET opa_decision_cache_hit_rate

# 2. If offline >5s (Read-only mode): FastAPI blocks writes
curl -X POST https://api.fellowgovernance.com/api/v1/diagnostic/start \
  -H "Authorization: Bearer <test_jwt>" \
  -H "Content-Type: application/json" \
  -d '{"agent":"A01","payload":{"test":true}}'
# Expected: HTTP 503 + {"error": "opa_unavailable", "mode": "read_only"}

# 3. Restart OPA sidecar (if hung)
kubectl rollout restart deployment/opa-gatekeeper -n fellow-gov

# 4. Reload policy bundle manually (if corruption suspected)
opa run -s -w policies/ --addr 0.0.0.0:8181 --log-level=debug
```

### ♻️ Recovery (15–30 min)
```bash
# 1. Verify OPA responds within SLO
time curl -s -X POST https://opa.internal:8181/v1/data/ceis/allow \
  -H "Content-Type: application/json" \
  -d '{"input":{"action":"test"}}' | jq .

# 2. Confirm fallback deactivation
psql -c "SELECT degraded_mode FROM audit_trails 
         ORDER BY timestamp DESC LIMIT 5;"
# Expected: all false

# 3. Resume writes (smoke test)
curl -X POST https://api.fellowgovernance.com/api/v1/diagnostic/start \
  -H "Authorization: Bearer <test_jwt>" \
  -H "Content-Type: application/json" \
  -d '{"agent":"A01","payload":{"test":true}}'
# Expected: HTTP 200 + {"run_id":"...","status":"processing"}
```

### ✅ Validation
```bash
# 1. Run OPA performance test
k6 run --env BASE_URL=https://api.fellowgovernance.com \
       --env JWT_TOKEN=<test_token> \
       /tests/perf/opa-eval-load.js

# 2. Confirm metrics return to SLO
# → Grafana: opa_eval_duration_seconds p99 < 2s
# → Alert: clear if no degraded_mode flags in last 10 min

# 3. Post-mortem
# → Document root cause in /docs/incidents/RB-001-<date>.md
# → Update runbook if new failure mode discovered (e.g., bundle size >500KB)
```

> 📌 **Fallback Behavior Summary**  
> • **Normal** (<2s): Full validation + `binding_token` issued  
> • **Degraded** (2s–5s): Cache hit + `degraded_mode=true` logged  
> • **Read-only** (>5s): BLOCKS writes, returns `503` for mutations  
> • **Recovery**: Auto-resumes when OPA health check passes 3x consecutively