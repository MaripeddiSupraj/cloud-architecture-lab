# Workload Characteristics

This document classifies the system before selecting products or deployment platforms.

| Workload | Traffic pattern | State model | Consistency need | Failure tolerance | Scaling tendency |
|---|---|---|---|---|---|
| Product browse | Read-heavy, bursty | Mostly stateless request path | Eventual freshness acceptable within agreed window | Degraded freshness may be acceptable | Horizontal + cache/edge |
| Search | Read-heavy, bursty | Indexed state | Eventual consistency from catalog | Search degradation must not corrupt commerce state | Horizontal query capacity |
| Cart | Frequent small reads/writes | Customer-scoped mutable state | Read-your-writes expected | Temporary unavailability impacts conversion | Horizontal, partitionable |
| Pricing | Read-heavy with business-rule evaluation | Versioned rule/config state | Checkout price must be authoritative | Stale checkout price not acceptable | Cache carefully |
| Inventory read | High-read during browse | Derived availability view | Small freshness window may be acceptable for browse | Stale browse view tolerable within policy | Cache/replica friendly |
| Inventory reservation | Burst-sensitive writes | Contended transactional state | Strong correctness required | Oversell risk must be controlled | Partition by SKU/domain |
| Checkout | Lower volume, high value | Workflow state | Strong state transitions | Must survive partial dependency failure | Horizontal with durable state |
| Orders | Transactional writes + reads | Durable system of record | Strong correctness | Loss/duplication unacceptable | Partitionable with relational needs |
| Payments | External integration | Durable reference/state | Idempotent transitions | Must reconcile uncertain outcomes | Isolate dependency |
| Notifications | Async burst | Durable delivery intent | At-least-once acceptable with dedupe | Must not block order commit | Queue-driven |
| Analytics | Async high-volume | Append/event oriented | Eventual | Customer path must remain isolated | Batch/stream independently |

## Derived architecture implications

- A single storage technology is unlikely to optimize every workload, but introducing a second data system requires clear operational value.
- Read scaling and transactional correctness should be solved independently where practical.
- Asynchronous execution is appropriate for work that does not need to complete inside the customer transaction.
- Checkout, order, inventory reservation, and payment state require explicit idempotency and state-transition rules.
- Search is a derived view, not the authoritative catalog record.
- Cache must never become the only durable source of transactional truth.
- External dependency failures must be isolated from internal resource exhaustion.

## Complexity budget

The default position is to choose the simplest architecture that meets measurable requirements.

A new distributed component is justified only if it solves a documented scale, resilience, latency, isolation, or delivery problem that cannot be handled adequately by an existing component.
