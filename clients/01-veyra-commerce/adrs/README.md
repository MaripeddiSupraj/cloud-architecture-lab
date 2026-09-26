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
| ADR-009 | Checkout and payment orchestration | Proposed |
| ADR-010 | Inventory reservation and consistency | Proposed |
| ADR-011 | Customer and workforce identity | Proposed |
| ADR-012 | Network topology and egress controls | Proposed |
| ADR-013 | Environment/account isolation | Proposed |
| ADR-014 | Deployment and release strategy | Proposed |
| ADR-015 | Observability architecture | Proposed |
| ADR-016 | Backup and disaster recovery | Proposed |
| ADR-017 | Infrastructure as code and configuration | Proposed |
| ADR-018 | Region strategy and expansion model | Proposed |

## ADR acceptance rule

An ADR must identify the decision context, measurable drivers, credible alternatives, why the selected option fits this workload, why alternatives were rejected or deferred, operational/cost consequences, Well-Architected pillar impact, validation, and revisit triggers.

A technology name without comparison or rationale is not an architecture decision.

Use [ADR-TEMPLATE.md](ADR-TEMPLATE.md) for new decisions.
