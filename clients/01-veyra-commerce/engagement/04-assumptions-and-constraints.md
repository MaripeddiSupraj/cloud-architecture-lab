# Assumptions and Constraints

## Delivery constraints

- Target launch window: approximately six months from architecture approval.
- Initial market: India.
- The platform will be operated by Veyra engineering rather than fully outsourced after launch.
- Existing enterprise systems may coexist with the new platform during migration.

## Team assumptions

Indicative delivery and operations organization:

| Discipline | Approximate size |
|---|---:|
| Backend engineering | 18 |
| Web/mobile engineering | 12 |
| Platform/DevOps | 4 |
| QA/SDET | 5 |
| Security | 2 |
| Data engineering/analytics | 4 |

Architecture complexity must be supportable by this team. Technologies requiring specialized operational expertise must demonstrate sufficient value to justify their adoption.

## Existing engineering skills

The team has working experience with Java, Node.js, PostgreSQL, Docker, CI/CD, and basic Kubernetes operations.

Deep expertise in large-scale event streaming and distributed database operations should not be assumed.

## Platform constraints

- Managed services are preferred where they materially reduce operational burden.
- Portability is desirable but must not override reliability, delivery speed, or economics without a concrete business reason.
- Infrastructure must be reproducible using infrastructure as code.
- Production changes require traceability through version control and delivery automation.
- Production data stores must not depend on single-instance designs.
- Secrets must not be embedded in repositories, images, pipeline definitions, or application configuration.

## Financial guardrail

The initial architecture should target a normal-operation cloud run rate in the approximate range of INR 20–25 lakh per month before exceptional marketing-event spikes and selected third-party SaaS charges.

This is a planning guardrail rather than a finalized budget. Cost estimates must state pricing assumptions, region, utilization, data-transfer assumptions, and excluded services.

## Explicit non-assumptions

At this stage we are **not** assuming:

- Kubernetes is the compute platform.
- Microservices are the application architecture.
- Kafka is required.
- A NoSQL database is required.
- Multi-region active-active is required.
- A service mesh is required.
- Every business capability needs an independently deployed service.

Each of these requires evidence and an ADR before adoption.
