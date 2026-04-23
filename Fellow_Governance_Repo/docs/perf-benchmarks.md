# Performance Benchmarks & Load Testing
**Version**: 1.0.0 | **Stack**: FastAPI + PostgreSQL + OPA + AgentScope | **Tool**: k6

## SLO Thresholds
| Endpoint / Flow | p95 Target | p99 Target | Error Rate Max | Timeout | Fallback Trigger |
|----------------|------------|------------|----------------|---------|------------------|
| `POST /api/v1/diagnostic/start` | 300ms | 800ms | 0.1% | 10s | Retry (3x) |
| `POST /api/v1/opa/eval` | 150ms | 1.8s | 0.05% | 2s | Degraded mode (cache) |
| `POST /api/v1/defense/start` | 10s (async) | 30s | 0.1% | 300s | `timeout_occurred=true` |
| `GET /api/v1/audit/{run_id}` | 120ms | 400ms | 0.05% | 5s | N/A |
| WebSocket `/ws/pipeline/{run_id}` | 50ms latency | 200ms | 0.5% drops | 60s idle | Reconnect exponential backoff |

## k6 Load Test Script
```javascript
// /tests/perf/fellow-governance-load.js
import http from 'k6/http';
import { check, group, sleep } from 'k6';
import { Trend, Rate } from 'k6/metrics';

const defensePipelineDuration = new Trend('defense_pipeline_duration', true);
const auditReadLatency = new Trend('audit_read_latency', true);
const errorRate = new Rate('error_rate');

export const options = {
  stages: [
    { duration: '30s', target: 10 },   // Ramp up
    { duration: '2m', target: 50 },    // Sustained load (matches global semaphore)
    { duration: '30s', target: 0 },    // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<300', 'p(99)<2000'],
    http_req_failed: ['rate<0.01'],
    defense_pipeline_duration: ['p(95)<300000'],
    error_rate: ['rate<0.01'],
  },
  summaryTrendStats: ['avg', 'min', 'med', 'max', 'p(90)', 'p(95)', 'p(99)'],
};

const BASE_URL = __ENV.BASE_URL || 'http://localhost:8000';
const JWT_TOKEN = __ENV.JWT_TOKEN || 'test-jwt-placeholder'; // Replace with real test token

export default function () {
  const headers = { 'Authorization': `Bearer ${JWT_TOKEN}`, 'Content-Type': 'application/json' };

  group('Diagnostic Pipeline', function () {
    const res = http.post(`${BASE_URL}/api/v1/diagnostic/start`, JSON.stringify({
      agent: 'A14',
      payload: { topic: 'PL 2338/2023', jurisdiction: 'BR' }
    }), { headers });
    check(res, { 'diagnostic status 200': (r) => r.status === 200 });
    errorRate.add(res.status >= 400);
  });

  group('OPA Evaluation', function () {
    const res = http.post(`${BASE_URL}/api/v1/opa/eval`, JSON.stringify({
      input: { user: 'lawyer_01', action: 'deploy_model', risk_score: 0.6 }
    }), { headers });
    check(res, { 'opa eval status 200': (r) => r.status === 200 });
    errorRate.add(res.status >= 400);
  });

  group('Defense Pipeline (Async)', function () {
    const start = new Date().getTime();
    const res = http.post(`${BASE_URL}/api/v1/defense/start`, JSON.stringify({
      case_text: 'ANPD violation notice #2026-041',
      evidence_files: ['model_report.csv', 'notice.pdf']
    }), { headers });
    check(res, { 'defense start 202': (r) => r.status === 202 });
    defensePipelineDuration.add(new Date().getTime() - start);
  });

  sleep(1);
}

Execution & Validation
# Run against staging
k6 run --env BASE_URL=https://staging.fellowgovernance.com --env JWT_TOKEN=$(aws secretsmanager get-secret-value --secret-id staging/jwt-test --query SecretString --output text) /tests/perf/fellow-governance-load.js

# Pass criteria
✅ p95 < 300ms for sync APIs
✅ p99 < 2s for OPA eval
✅ Defense pipeline respects 300s timeout
✅ Error rate < 1% under 50 concurrent tenants
✅ OPA fallback triggers at >2s latency (verified via Grafana `opa_eval_duration_seconds`)

Bottleneck Mitigation

Symptom
Root Cause
Fix
p99 > 2s on OPA eval
Policy bundle size > 500KB / missing index
Split .rego by domain; cache opa eval results in Redis
Defense pipeline hits 300s
AgentScope LLM context window overflow
Truncate kb_state vectors; enable asyncio.gather() with timeout per agent
http_req_failed > 1%
DB connection pool exhaustion
Tune SQLALCHEMY_POOL_SIZE to max_connections / tenants; add PgBouncer


---

### 📂 `/docs/runbooks/`

#### `RB-001-OPA-DEGRADED.md`
```markdown
# Runbook RB-001: OPA Degraded/Offline
**Severity**: P1 → P0 | **Trigger**: `opa_eval_timeout_rate > 0.1` or `opa_health_check_fail`

## Triage (0-5 min)
1. Check OPA health: `curl -s https://opa.internal:8181/v1/system/health`
2. Verify policy bundle version: `curl -s https://opa.internal:8181/v1/bundles/fellow-gov | jq .version`
3. Check fallback activation: Query `audit_trails` for `degraded_mode=true` in last 10 min.

## Mitigation (5-15 min)
- If timeout 2s–5s: System auto-switches to Redis cache. Verify cache hit rate: `redis-cli GET opa_decision_cache_hit_rate`
- If offline >5s: FastAPI blocks writes (`503`). Confirm via `curl -X POST https://api.fellowgovernance.com/api/v1/diagnostic/start -H "Authorization: Bearer <test>"`

## Recovery (15-30 min)
1. Restart OPA sidecar: `kubectl rollout restart deployment/opa-gatekeeper -n fellow-gov`
2. Reload bundle: `opa run -s -w policies/ --addr 0.0.0.0:8181`
3. Verify: `curl -X POST https://api.fellowgovernance.com/api/v1/opa/eval -d '{"input":{"action":"test"}}' -H "Authorization: Bearer <test>"` → Expect `{ "allow": true }`
4. Monitor: Grafana `opa_eval_duration_seconds` returns to `<2s` p95.

## Validation
- Run k6 OPA test: `k6 run --env BASE_URL=https://api.fellowgovernance.com /tests/perf/fellow-governance-load.js`
- Confirm `degraded_mode` flags stop appearing in `audit_trails`.