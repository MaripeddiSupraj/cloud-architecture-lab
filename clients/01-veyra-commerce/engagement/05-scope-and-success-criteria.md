# Scope and Success Criteria

## In scope

The architecture engagement covers:

- Customer-facing web and mobile platform architecture.
- API and application architecture.
- Transactional, catalog, search, cache, and event data flows.
- Payment integration boundaries.
- Inventory and order consistency patterns.
- Network and ingress/egress design.
- Identity, secrets, encryption, and security controls.
- Infrastructure-as-code and environment strategy.
- CI/CD and production release patterns.
- Observability and operational readiness.
- Backup, restoration, disaster recovery, and resilience testing.
- Capacity planning and scaling behaviour.
- Cost model and optimization opportunities.
- Architecture Decision Records for major technology and pattern choices.

## Out of scope for the initial architecture phase

- Designing the payment provider's internal platform.
- Warehouse automation hardware.
- Courier-provider internal systems.
- ERP replacement.
- Detailed UI/UX design.
- Recommendation-model development.
- Enterprise-wide data warehouse modernization.

Interfaces to these capabilities remain in scope where they affect reliability, security, data consistency, or customer experience.

## Architecture success criteria

The architecture is considered ready for implementation when:

1. Critical business journeys have explicit system and data flows.
2. Capacity assumptions and scaling limits are documented.
3. Major technology choices have accepted ADRs.
4. Transaction boundaries, idempotency, and failure behaviour are defined for checkout, payment, inventory, and order creation.
5. No production-critical component has an unexplained single point of failure.
6. Security trust boundaries and privileged access paths are documented.
7. Recovery objectives map to concrete backup, replication, and restoration mechanisms.
8. Delivery and rollback paths are defined for production.
9. Observability covers both system health and critical business outcomes.
10. Estimated cost is traceable to workload assumptions.
11. The architecture has been challenged against agreed failure and peak-load scenarios.

## Architecture review gates

The engagement will use four review gates:

- **Gate A — Requirements:** business, NFR, constraints, assumptions, capacity baseline.
- **Gate B — Decisions:** architecture style, data, compute, integration, security, networking.
- **Gate C — Production design:** deployment, observability, resilience, DR, FinOps.
- **Gate D — Readiness:** threat review, failure exercises, runbooks, implementation backlog.

A gate may expose unresolved decisions; it must not hide them behind a diagram.
