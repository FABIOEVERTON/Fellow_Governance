# Threat Model: Fellow Governance (STRIDE)
**Version**: 1.0.0 | **Last Reviewed**: 2026-04-21 | **Scope**: Production SaaS

## STRIDE Matrix

| Threat Category | Component / Attack Vector | Mitigation (Implemented) | Detection | Residual Risk |
|----------------|---------------------------|--------------------------|-----------|---------------|
| **Spoofing** | JWT forgery / tenant impersonation | RS256 JWT validation + `tenant_id` claim enforcement + PostgreSQL RLS (`SET LOCAL app.current_tenant`) | Alert on `jwt_validation_failure` > 5/min; OPA `deny` logs with `reason: "invalid_tenant"` | Low: Requires compromise of signing key. Rotated every 90d via Vault. |
| **Tampering** | Audit trail rewrite / policy hash alteration | `NO_TRAIL_REWRITE` invariant (DB `REVOKE UPDATE ON audit_trails`); SHA-256 chain validation on read; `CODE_IDENTITY` invariant blocks deployment if `sha256(policy) != deployed_hash` | Prometheus `audit_chain_integrity_check_fail` alert; GPG verification failure on Forensic ZIP | None: Mathematically bound. Break triggers `LOCK_SESSION`. |
| **Repudiation** | User denies execution or disputes evidence | Append-only `audit_trails` with `prev_hash` chaining; Forensic ZIP signed via `python-gnupg`; `run_id` immutable across SEK, agents, OPA | Automated weekly `audit_trails` hash verification cron; OPA `binding_token` audit trail cross-check | Low: Requires insider threat with `auditor_supervisor` role + recovery token forgery. |
| **Information Disclosure** | Cross-tenant data leak / PII exposure in logs | Row-Level Security (RLS) on all 16 tables; `tenant_id` injected by middleware; `structlog` masks PII fields; `kb_state` vectors expire via 90d TTL | DB query logs filtered; Grafana `cross_tenant_join_attempts` counter; Log ingestion scrubber | Low: RLS + middleware isolation prevents app-layer leaks. |
| **Denial of Service** | Worker exhaustion / OPA timeout cascade / tenant resource hogging | Per-tenant semaphore (max 1) + global semaphore (max 5) for defense pipeline; `RESOURCE_CAP` invariant; Rate limits per endpoint (10/min diagnostic, 3/min defense); OPA fallback (cache → read-only) | Prometheus `defense_pipeline_queue_depth`, `opa_eval_timeout_rate`, `http_rate_limit_exceeded` | Medium: Sustained multi-tenant attack may hit global semaphore. Mitigated by auto-scaling + WAF. |
| **Elevation of Privilege** | LLM bypassing PCG / direct DB write / admin escalation | `AI_NO_MUTATION` invariant (LLM agents receive read-only Pydantic contracts; SEK executes writes); PCG binding validation (`allow==true` + JWT token); RBAC with least privilege | OPA `policy_violation` alerts; DB audit triggers on `INSERT/UPDATE` without `SEK` session tag | Low: Architecture enforces separation of concerns. No direct LLM→DB path exists. |

## Control Coverage Map
| Invariant | STRIDE Category Addressed | Implementation |
|-----------|--------------------------|----------------|
| `DATA_LINEAGE` | Tampering, Repudiation | SHA-256 append-only chain in `audit_trails` |
| `OPA_FINALITY` | Elevation of Privilege, Spoofing | No state transition without `allow==true` + `binding_token` |
| `AI_NO_MUTATION` | Elevation of Privilege, Tampering | LLM outputs validated by Pydantic; SEK writes only |
| `DEFENSE_TIMER` + Semaphores | DoS | Hard timeout (300s) + concurrency limits per tenant |
| `RESOURCE_CAP` | DoS, Information Disclosure | CPU/Mem quotas + RLS isolation |
| `NO_TRAIL_REWRITE` | Tampering, Repudiation | DB `REVOKE UPDATE` + append-only enforcement in SEK |

## Review Cadence
- **Pre-deploy**: OPA policy + adapter contract diff review.
- **Monthly**: RLS policy audit + hash chain integrity scan.
- **Quarterly**: Full STRIDE re-assessment + penetration test scope alignment.