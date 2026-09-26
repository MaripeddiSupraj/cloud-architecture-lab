# Initial AWS Platform HLD

This is the first implementation-level architecture derived from accepted ADRs. It is intentionally incomplete: identity, detailed VPC topology, observability, secrets/KMS, CI/CD, backup/DR, and checkout orchestration remain separate decisions.

## Current selected building blocks

| Concern | Selected approach | Decision |
|---|---|---|
| Application shape | Coarse-grained domain services | [ADR-001](../adrs/ADR-001-application-decomposition.md) |
| Compute | Amazon ECS on AWS Fargate | [ADR-002](../adrs/ADR-002-container-platform.md) |
| Edge / public ingress | CloudFront + AWS WAF + ALB | [ADR-003](../adrs/ADR-003-edge-and-api-ingress.md) |
| Transactional database | RDS for PostgreSQL Multi-AZ | [ADR-004](../adrs/ADR-004-primary-transactional-database.md) |
| Product search | Amazon OpenSearch Service | [ADR-005](../adrs/ADR-005-product-search.md) |
| Distributed cache | ElastiCache Serverless for Valkey | [ADR-006](../adrs/ADR-006-cache-platform.md) |
| Async work / domain events | SQS + EventBridge | [ADR-007](../adrs/ADR-007-async-messaging-and-events.md) |
| Product/static media | Amazon S3 | [ADR-008](../adrs/ADR-008-product-media-storage.md) |

## Request path

1. Customer traffic enters through CloudFront.
2. AWS WAF policies protect the public edge.
3. Static/media requests are served through CloudFront from private S3 origins where possible.
4. Dynamic application traffic reaches the Application Load Balancer.
5. ALB routes requests to ECS/Fargate domain services.
6. Services use PostgreSQL for authoritative transactions, Valkey for safe cacheable/ephemeral data, and OpenSearch for product discovery.
7. Domain events and background work leave the synchronous path through EventBridge/SQS.
8. Payment, warehouse, delivery, and notification providers are treated as external failure domains.

## Important boundaries

- OpenSearch is a derived index, never catalog transaction truth.
- ElastiCache is never the sole durable record for orders/payments/inventory.
- Notifications and analytics must not block order commit.
- Payment uncertainty requires idempotency and reconciliation.
- A service may not introduce a separate datastore simply because it is independently deployed.
- Direct external access to data services is prohibited.

## Deliberately unresolved

The diagram must not imply decisions that have not yet been made. The following remain open:

- customer identity and workforce identity,
- exact VPC/subnet/NAT/endpoint design,
- secrets and KMS key strategy,
- checkout/payment orchestration,
- inventory reservation algorithm,
- CI/CD and deployment strategy,
- observability stack and SLO implementation,
- backup/restore and cross-region DR,
- cost model and scaling thresholds.
