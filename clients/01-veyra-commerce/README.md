# Client 01 — Veyra Commerce

**Domain:** Digital commerce  
**Channels:** Web, iOS, Android  
**Primary launch market:** India  
**Engagement phase:** Production design / implementation readiness

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
- [Golden production reference architecture](diagrams/09-production-reference-architecture.eraserdiagram)

The diagram set includes system context, AWS HLD, network/security, checkout/payment, payment recovery, data/events, CI/CD, observability, and disaster recovery.

## Governance and quality

- [Well-Architected review model](governance/01-well-architected-review-model.md)
- [Service decision standard](governance/02-service-decision-standard.md)
- [Initial six-pillar review](reviews/01-well-architected-initial-review.md)
- [Threat model](security/01-threat-model.md)

## Performance and cost

- [Initial capacity model](capacity/01-initial-capacity-model.md)
- [Traffic breakdown](capacity/02-traffic-breakdown.md)
- [ECS service sizing](capacity/03-service-sizing.md)
- [Database capacity and connection model](capacity/04-database-capacity.md)
- [Data transfer and telemetry model](capacity/05-data-transfer-and-telemetry.md)
- [Capacity validation review](reviews/02-capacity-validation.md)
- [Peak event readiness](performance/01-peak-event-readiness.md)
- [FinOps cost model and guardrails](finops/01-cost-model-and-guardrails.md)
- [AWS pricing workbook](finops/02-pricing-workbook.md)
- [Cost optimization decisions](finops/03-cost-optimization-decisions.md)
- [Planning-grade monthly cost estimate](finops/04-planning-grade-cost-estimate.md)

## Architecture principle

No box exists on a diagram merely because it is a popular AWS service.

For every major component the repository records:
**requirement -> workload -> options -> trade-offs -> decision -> validation -> revisit trigger**.

The capacity model now contains a first numerical workload envelope and service-sizing hypothesis. The next evidence gate is **benchmark + calculator validation**: representative ECS load tests, PostgreSQL connection/IO tests, OpenSearch and Valkey benchmarks, media/CDN transfer measurement, Cognito feature-tier confirmation, and an AWS Pricing Calculator estimate for Mumbai/Hyderabad. Those measurements may change accepted sizing or even reopen an ADR.


## Implementation and readiness

- [Terraform implementation blueprint](implementation/01-terraform-blueprint.md)
- [Failure experiment plan](reliability/01-failure-experiment-plan.md)
- [Production readiness checklist](readiness/01-production-readiness-checklist.md)

## Current maturity

The architecture is now complete enough for an implementation prototype. The remaining evidence is deliberately operational: benchmark the sizing assumptions, reproduce the AWS Calculator estimate, deploy the Terraform slices in non-production, execute failure experiments, and close the readiness checklist before treating the design as production-approved.
