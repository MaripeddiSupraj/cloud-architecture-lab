# Client 01 — Veyra Commerce

**Domain:** Digital commerce  
**Channels:** Web, iOS, Android  
**Primary launch market:** India  
**Engagement phase:** Architecture definition / production design

## Engagement objective

Design a secure, resilient, scalable, and economically sustainable digital commerce platform that Veyra's engineering organization can operate and evolve over a multi-year horizon.

Architecture decisions start from requirements and workload characteristics. AWS products are selected only when the decision record explains why the service fits Veyra and why credible alternatives do not fit as well today.

## Start here

1. [Business context](engagement/01-business-context.md)
2. [Initial capacity model](capacity/01-initial-capacity-model.md)
3. [Workload characteristics](architecture/01-workload-characteristics.md)
4. [Initial AWS platform HLD](architecture/02-initial-aws-platform-hld.md)
5. [Why this / why not that service matrix](architecture/03-service-selection-matrix.md)
6. [ADR decision register](adrs/README.md)
7. [Eraser diagram set](diagrams/README.md)
8. [Initial Well-Architected review](reviews/01-well-architected-initial-review.md)

## Engagement

- [Business context](engagement/01-business-context.md)
- [Business requirements](engagement/02-business-requirements.md)
- [Non-functional requirements](engagement/03-non-functional-requirements.md)
- [Assumptions and constraints](engagement/04-assumptions-and-constraints.md)
- [Scope and success criteria](engagement/05-scope-and-success-criteria.md)
- [Discovery question register](engagement/06-open-questions.md)
- [Architecture risk register](engagement/07-risk-register.md)

## Architecture and diagrams

- [Workload characteristics](architecture/01-workload-characteristics.md)
- [Initial AWS platform HLD](architecture/02-initial-aws-platform-hld.md)
- [Service selection matrix](architecture/03-service-selection-matrix.md)
- [All Eraser diagrams](diagrams/README.md)

The diagram set includes system context, AWS HLD, network/security, checkout/payment, payment recovery, data/events, CI/CD, observability, and disaster recovery.

## Governance and quality

- [Well-Architected review model](governance/01-well-architected-review-model.md)
- [Service decision standard](governance/02-service-decision-standard.md)
- [Initial six-pillar review](reviews/01-well-architected-initial-review.md)
- [Threat model](security/01-threat-model.md)

## Performance and cost

- [Initial capacity model](capacity/01-initial-capacity-model.md)
- [Peak event readiness](performance/01-peak-event-readiness.md)
- [FinOps cost model and guardrails](finops/01-cost-model-and-guardrails.md)

## Architecture principle

No box exists on a diagram merely because it is a popular AWS service.

For every major component the repository records:
**requirement -> workload -> options -> trade-offs -> decision -> validation -> revisit trigger**.

The next design maturity step is implementation-level sizing and pricing: per-domain traffic distribution, representative ECS resource benchmarks, database connection/IO profile, CDN transfer/cache ratio, search/cache load, telemetry volume, and a dated Mumbai/Hyderabad AWS pricing estimate.
