# Architecture Decision Records

ADRs capture decisions that materially affect system structure, operational burden, security, reliability, cost, or future change.

## Status values

- **Proposed** — under evaluation.
- **Accepted** — approved for implementation.
- **Superseded** — replaced by a later ADR.
- **Rejected** — option considered and intentionally not selected.

## Decision register

| ADR | Decision | Status |
|---|---|---|
| [ADR-000](ADR-000-aws-implementation-baseline.md) | AWS implementation baseline | Accepted |
| [ADR-001](ADR-001-application-decomposition.md) | Application decomposition | Accepted |
| [ADR-002](ADR-002-container-platform.md) | Primary compute platform | Accepted |
| [ADR-003](ADR-003-edge-and-api-ingress.md) | Edge delivery and API ingress | Accepted |
| [ADR-004](ADR-004-primary-transactional-database.md) | Primary transactional database | Accepted |
| [ADR-005](ADR-005-product-search.md) | Product search platform | Accepted |
| [ADR-006](ADR-006-cache-platform.md) | Distributed cache platform | Accepted |
| [ADR-007](ADR-007-async-messaging-and-events.md) | Async messaging and domain events | Accepted |
| [ADR-008](ADR-008-product-media-storage.md) | Product/static media storage | Accepted |
| [ADR-009](ADR-009-checkout-payment-orchestration.md) | Checkout and payment orchestration | Accepted |
| [ADR-010](ADR-010-inventory-reservation.md) | Inventory reservation and oversell control | Accepted |
| [ADR-011](ADR-011-identity-secrets-encryption.md) | Identity, workload credentials, secrets and encryption | Accepted |
| [ADR-012](ADR-012-network-topology.md) | Production network topology and egress | Accepted |
| [ADR-013](ADR-013-account-environment-isolation.md) | AWS account and environment isolation | Accepted |
| [ADR-014](ADR-014-deployment-release-strategy.md) | CI/CD and production release strategy | Accepted |
| [ADR-015](ADR-015-observability.md) | Observability and service health | Accepted |
| [ADR-016](ADR-016-disaster-recovery.md) | Regional disaster recovery strategy | Accepted |
| [ADR-017](ADR-017-infrastructure-as-code.md) | Infrastructure as Code and configuration | Accepted |
| [ADR-018](ADR-018-region-strategy.md) | Region and geographic expansion strategy | Accepted |
| [ADR-019](ADR-019-scaling-capacity-strategy.md) | Scaling and peak-event capacity strategy | Accepted |
| [ADR-020](ADR-020-cognito-feature-tier.md) | Cognito feature tier | Accepted |

## ADR acceptance rule

Every ADR must identify:
- decision context and measurable drivers,
- credible alternatives,
- why the selected option fits this workload,
- why alternatives were rejected or deferred,
- operational and cost consequences,
- Well-Architected pillar impact,
- validation evidence,
- conditions that trigger reconsideration.

A technology name without comparison or rationale is not an architecture decision.

Use [ADR-TEMPLATE.md](ADR-TEMPLATE.md) for future decisions.
