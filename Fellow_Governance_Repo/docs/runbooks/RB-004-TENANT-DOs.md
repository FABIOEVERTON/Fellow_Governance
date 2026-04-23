## 🟡 RB-004: Tenant DoS / Resource Exhaustion

| Metadata | Value |
|----------|-------|
| **Severity** | 🟡 P2 → P1 (Escalating) |
| **Trigger** | `http_rate_limit_exceeded` > 50/min OR `tenant_cpu_usage > 90%` |
| **Impact** | Tenant throttled or blocked at WAF |
| **SLA** | Restore normal traffic in ≤30 min |
| **Invariant** | `RESOURCE_CAP` |

### 🚨 Triage (0–5 min)
```bash
# 1. Identify offending tenant
psql -c "SELECT tenant_id, COUNT(*) FROM http_requests 
         WHERE status=429 AND timestamp > NOW() - INTERVAL '5 min' 
         GROUP BY tenant_id ORDER BY COUNT DESC LIMIT 5;"

# 2. Check RESOURCE_CAP enforcement
redis-cli GET tenant:<UUID>:quota_exceeded

# 3. Verify rate limit headers
curl -s -I https://api.fellowgovernance.com/api/v1/diagnostic/start \
  -H "Authorization: Bearer <tenant_jwt>" | grep X-RateLimit
```

### 🛠️ Mitigation (5–15 min)
```bash
# 1. Auto-throttle active. Confirm via headers
# Expected: X-RateLimit-Remaining: 0

# 2. If abusive: Block at WAF level
aws wafv2 create-ip-set \
  --name block-<UUID> \
  --ip-address-range 0.0.0.0/0 \
  --scope REGIONAL \
  --region sa-east-1

# 3. Notify tenant via feature_flags consent record
psql -c "SELECT consent_record FROM feature_flags 
         WHERE tenant_id='UUID_HERE' AND flag_key='notifications';"
```

### ♻️ Recovery (15–30 min)
```bash
# 1. Clear temporary blocks if legitimate traffic
redis-cli DEL tenant:<UUID>:rate_limit

# 2. Increase quota if plan allows
psql -c "UPDATE tenants SET plan='enterprise' WHERE id='UUID_HERE';"

# 3. Monitor resource usage
# → Grafana: tenant_cpu_usage returns to <70%
# → Alert: clear if no 429 responses in last 10 min
```

### ✅ Validation
```bash
# 1. Run k6 rate limit test
k6 run --env TENANT_TOKEN=<UUID> /tests/perf/ratelimit-test.js

# 2. Confirm 429 responses stop after quota reset
curl -s -I https://api.fellowgovernance.com/api/v1/diagnostic/start \
  -H "Authorization: Bearer <tenant_jwt>" | grep "HTTP/2 200"

# 3. Post-mortem
# → Document root cause in /docs/incidents/RB-004-<date>.md
# → Consider: adjust rate limits per endpoint?
```

> 📌 **Rate Limits by Endpoint**  
> • `POST /diagnostic/start`: 10/min  
> • `POST /defense/start`: 3/min  
> • `POST /opa/eval`: 100/min  
> • `POST /policies/compile`: 20/hour  
> • `GET /audit/{run_id}`: 200/min  
> • Exceed → HTTP 429 + `{"error": "rate_limit_exceeded", "retry_after": seconds}`