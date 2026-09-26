# Architecture Risk Register

| ID | Risk | Likelihood | Impact | Current treatment | Owner |
|---|---|---|---|---|---|
| R-001 | Promotional traffic exceeds scaling speed and overloads origin systems | Medium | High | Capacity model, load tests, pre-scaling where justified, backpressure | Architecture / Platform |
| R-002 | Payment succeeds externally but internal order state is not committed | Medium | Critical | Idempotency, durable workflow state, reconciliation design | Commerce |
| R-003 | Concurrent purchases oversell constrained inventory | Medium | High | Define reservation and concurrency model before implementation | Commerce |
| R-004 | External provider latency consumes application threads/connections | High | High | Timeouts, isolation, bounded retries, circuit breaking where appropriate | Platform |
| R-005 | Architecture introduces more distributed systems than team can operate | Medium | High | Complexity budget and ADR review | Architecture |
| R-006 | Search index becomes inconsistent with catalog source of truth | Medium | Medium | Event/CDC synchronization plus repair/reindex path | Catalog |
| R-007 | Observability volume causes unexpected monthly spend | Medium | Medium | Telemetry budgets, sampling, retention tiers | Platform / FinOps |
| R-008 | Region-level outage exceeds recovery objectives | Low/Medium | Critical | DR design tied to confirmed business requirement | Architecture |
| R-009 | Legacy coexistence produces conflicting sources of truth | Medium | High | Domain ownership and migration cutover rules | Architecture / Product |
| R-010 | Secret or privileged credential leakage through CI/CD | Medium | Critical | Workload identity, secret scanning, short-lived credentials | Security |
| R-011 | Bot or abusive traffic consumes scarce sale inventory or origin capacity | High | High | Edge controls, rate policy, bot strategy, business fairness rules | Security / Commerce |
| R-012 | Database connection exhaustion occurs before compute saturation | Medium | High | Connection budget, pooling, load testing | Platform |
| R-013 | Rollback is unsafe after backward-incompatible schema change | Medium | High | Expand/contract migrations and release compatibility rules | Engineering |
| R-014 | Backup exists but restoration cannot meet RTO | Medium | Critical | Scheduled restore verification and recovery exercises | Platform |
| R-015 | Normal-day infrastructure is overbuilt for rare events | Medium | Medium | FinOps model separates baseline from event capacity | FinOps |

## Risk handling

Risks marked Critical or High impact must be addressed by an explicit design control, accepted by an accountable owner, or documented as residual risk before production readiness.
