# Failure Experiment Plan

## Goal

Prove the architecture's failure behaviour before production rather than relying on diagrams and service SLAs.

Experiments begin in non-production with production-like topology. Controlled production game days are considered only after safeguards and rollback are proven.

## Experiment 1 — ECS task loss

**Inject**
- terminate 30–50% of tasks for one request-serving service.

**Expected**
- ALB stops routing to unhealthy tasks,
- remaining tasks absorb load within SLO,
- ECS replaces failed tasks,
- no customer transaction duplication.

**Evidence**
- recovery time,
- error rate,
- scale-out time,
- downstream saturation.

## Experiment 2 — Availability Zone loss

**Inject**
- remove one AZ's application capacity from the test path.

**Expected**
- remaining AZs continue serving,
- no dependence on a single NAT/app subnet,
- database remains available through Multi-AZ behaviour.

**Evidence**
- customer error/latency,
- connection recovery,
- provider egress continuity.

## Experiment 3 — RDS failover

**Inject**
- planned Multi-AZ failover under checkout load.

**Expected**
- brief connection interruption only,
- connection pools recover,
- idempotent requests retry safely,
- no duplicate order/payment state.

**Evidence**
- failover duration,
- failed transactions,
- retry success,
- outbox continuity.

## Experiment 4 — Cache unavailable

**Inject**
- deny/interrupt Valkey access.

**Expected**
- application remains correct,
- latency/load increases within controlled limits,
- PostgreSQL protection/backpressure prevents collapse,
- cache rebuild does not create stampede.

## Experiment 5 — OpenSearch unavailable

**Inject**
- block search access or return errors.

**Expected**
- search functionality degrades explicitly,
- catalog/order truth remains unaffected,
- customer checkout remains available,
- indexing backlog is preserved/replayed.

## Experiment 6 — Payment provider timeout

**Inject**
- return latency beyond client timeout after simulated provider authorization.

**Expected**
- payment becomes UNKNOWN/PENDING_CONFIRMATION,
- customer retry with same idempotency key does not create a second charge,
- callback/reconciliation resolves state.

## Experiment 7 — Duplicate callbacks

**Inject**
- deliver same payment callback multiple times and out of order.

**Expected**
- one logical transition,
- one logical OrderPaid outcome,
- no duplicated fulfilment.

## Experiment 8 — SQS consumer outage

**Inject**
- stop one consumer class for 30–60 minutes.

**Expected**
- queue buffers work,
- customer transaction path stays healthy,
- age/backlog alarms fire,
- restarted workers drain backlog without duplicate business side effects.

## Experiment 9 — Telemetry overload

**Inject**
- temporary high log/error volume.

**Expected**
- telemetry cost/throughput controls hold,
- application is not blocked on synchronous log delivery,
- alerting remains usable rather than noisy.

## Experiment 10 — Regional DR game day

**Inject**
- declare simulated Mumbai region unavailable.

**Expected**
- verify replica lag,
- promote Hyderabad database,
- scale recovery application,
- load regional secrets/images,
- switch customer routing,
- verify payment/ERP/courier integration,
- measure actual RPO/RTO.

## Pass/fail rule

An experiment passes only when:
- customer/business outcome matches the documented design,
- alerting detects the failure,
- responders have a usable runbook,
- recovery completes inside the relevant SLO/RTO,
- no hidden data inconsistency remains afterward.

Every failed experiment creates either:
- an architecture change,
- an operational change,
- a revised requirement,
- or an explicitly accepted residual risk.
