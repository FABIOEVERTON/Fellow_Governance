## 🔗 RB-002: Hash Chain Break (DATA_LINEAGE Invariant)

| Metadata | Value |
|----------|-------|
| **Severity** | 🔴 P0 (Critical) |
| **Trigger** | `prev_hash != compute_hash(prev)` in `audit_trails` |
| **Impact** | System enters `LOCK_SESSION`; all writes blocked |
| **SLA** | Resolve in ≤30 min |
| **Invariant** | `DATA_LINEAGE`, `NO_TRAIL_REWRITE` |

### 🚨 Triage (0–5 min)
```bash
# 1. Verify lock state
curl -s https://api.fellowgovernance.com/api/v1/system/status | jq .lock_state
# Expected: {"lock_state": "LOCK_SESSION", "reason": "hash_chain_break"}

# 2. Identify break point
psql -c "SELECT id, run_id, prev_hash, payload_hash, timestamp 
         FROM audit_trails 
         WHERE status='broken' 
         ORDER BY timestamp DESC LIMIT 1;"

# 3. Notify stakeholders
# → P0 alert auto-sent to #governance-p0 (Slack) + tenant admin email
```

### 🛠️ Mitigation (5–15 min)
```bash
# 1. Rotate signing key (if compromise suspected)
vault write -f fellow-gov/gpg/key

# 2. Generate recovery token (Python one-liner)
python3 -c "
import jwt, datetime, hashlib, os
token = jwt.encode({
  'sub': 'auditor_supervisor',
  'tenant_id': 'UUID_HERE',
  'exp': datetime.datetime.utcnow() + datetime.timedelta(hours=1),
  'recovery_nonce': hashlib.sha256(b'break-$(date +%Y%m%d)').hexdigest()
}, os.getenv('RECOVERY_PRIVATE_KEY'), algorithm='RS256')
print(token)
"

# 3. Insert recovery event
psql -c "INSERT INTO recovery_events 
         (tenant_id, previous_prev_hash, new_prev_hash, recovery_token, operator_id) 
         VALUES ('UUID_HERE', 'OLD_HASH', 'NEW_HASH', 'TOKEN_HERE', 'OP_ID');"
```

### ♻️ Recovery (15–30 min)
```bash
# 1. Seal new chain (system auto-computes)
# prev_hash = SHA256("RECOVERY_AFTER_BREAK_" + timestamp + recovery_token)

# 2. Verify LOCK_SESSION lifted
curl -s https://api.fellowgovernance.com/api/v1/system/status | jq .lock_state
# Expected: {"lock_state": "NORMAL"}

# 3. Resume writes (smoke test)
curl -X POST https://api.fellowgovernance.com/api/v1/diagnostic/start \
  -H "Authorization: Bearer <test_jwt>" \
  -H "Content-Type: application/json" \
  -d '{"agent":"A01","payload":{"test":true}}'
# Expected: HTTP 200 + {"run_id":"...","status":"processing"}
```

### ✅ Validation
```bash
# 1. Run integrity checker
python scripts/verify_hash_chain.py --tenant UUID_HERE
# Expected: "Chain integrity: OK (N links verified)"

# 2. Confirm append-only enforcement
psql -c "SELECT COUNT(*) FROM audit_trails 
         WHERE operation='UPDATE' AND timestamp > NOW() - INTERVAL '1 hour';"
# Expected: 0

# 3. Post-mortem
# → Document root cause in /docs/incidents/RB-002-<date>.md
# → Update runbook if new failure mode discovered
```

> 📌 **Hash Chain Rules**  
> • `prev_hash = SHA256(previous.payload_hash + previous.timestamp)`  
> • Break → `LOCK_SESSION` → recovery via `recovery_events` table  
> • Never rewrite `audit_trails` (`NO_TRAIL_REWRITE` invariant)