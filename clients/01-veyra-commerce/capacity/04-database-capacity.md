# PostgreSQL Capacity and Connection Model

## Purpose

The database is sized from **transaction pressure and connections**, not from the platform's 15k web RPS.

Browse/search/media load is intentionally offloaded through cache, OpenSearch and CloudFront.

## Critical transactional envelope

Initial stress targets:

| Workload | Stress target |
|---|---:|
| Completed orders | 75/sec |
| Inventory reservation attempts | 150/sec |
| Cart durable writes | 300/sec |
| Customer/profile writes | 50/sec |
| Payment/order state transitions | 150/sec |
| Outbox inserts/claims | 150+/sec |

Read traffic must use indexes and cache where safe so customer browse load does not dominate OLTP.

## Connection budget

A common failure mode is ECS scaling faster than PostgreSQL connection capacity.

### Naive configuration — rejected

If 150 application tasks each open a pool of 20 connections:

```
150 × 20 = 3,000 database connections
```

That is not an acceptable default.

### Initial connection policy

Start with small pools:

| Service | Max DB connections/task |
|---|---:|
| Catalog/Pricing | 4–6 |
| Cart | 4–6 |
| Checkout/Orders | 6–8 |
| Inventory | 6–8 |
| Workers | 2–4 |

A peak deployment must keep total application connection allowance below a tested safe database ceiling with reserve for:
- migrations,
- operations,
- monitoring,
- failover/reconnect storms.

## RDS Proxy decision

**Not automatically added.**

Evaluate RDS Proxy when tests show:
- connection churn during Fargate scaling,
- failover causes harmful reconnect storms,
- application pools consume too much DB connection headroom,
- many short-lived connections dominate database resources.

Why not add it by default:
- it is another managed component and cost layer,
- well-behaved long-lived application pools may already be sufficient,
- proxy semantics/transaction behavior must be tested for the application.

## Initial instance-class validation

Benchmark a memory-optimized or general-purpose Graviton RDS PostgreSQL class appropriate to the dataset and transaction profile rather than committing to a final size from documentation.

The benchmark must include:
- 75 committed orders/sec,
- 150 inventory reservation attempts/sec,
- cart writes,
- outbox writes/reads,
- realistic indexes,
- realistic dataset size,
- Multi-AZ deployment,
- failover test.

## Storage model

Current transactional dataset: ~1.2 TB.

Plan for:
- primary data,
- indexes,
- table/index bloat headroom,
- WAL/maintenance behavior,
- automated backup retention,
- point-in-time recovery,
- cross-Region replica,
- three-year growth.

Do not size storage at exactly current data volume.

## Query SLOs

Representative targets under peak load:
- inventory reservation transaction: P95 < 100 ms,
- order state write: P95 < 100 ms,
- cart durable write: P95 < 100 ms,
- indexed order/customer read: P95 < 50–100 ms,
- long-running analytical queries: prohibited on primary OLTP path.

## Failure validation

Test:
1. Multi-AZ failover during active checkout.
2. Connection reconnect behavior.
3. Transaction retry safety.
4. Outbox correctness through failover.
5. Reservation idempotency.
6. Replica lag under write peak.
7. restore from backup/PITR.

## Decision rule

Scale the database only after identifying the limiting resource:
- CPU,
- memory/cache hit,
- IOPS,
- storage throughput,
- connection count,
- lock contention,
- bad query/index design.

"Database is slow" is not a valid scaling reason without evidence.
