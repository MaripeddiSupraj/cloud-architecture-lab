# Service Decision Standard

Every significant service selection must answer two questions:

1. **Why this option for this workload?**
2. **Why not the credible alternatives?**

A diagram box without this reasoning is not considered an architecture decision.

## Required evaluation dimensions

| Dimension | Required evidence |
|---|---|
| Requirement fit | Which functional/NFR requirement does the option satisfy? |
| Workload fit | Traffic shape, state model, consistency, latency, throughput, data size |
| Reliability | AZ/region failure behavior, durability, recovery, dependency isolation |
| Performance | Scaling unit, bottlenecks, latency characteristics, concurrency limits |
| Security | Identity model, encryption, network exposure, secrets, auditability |
| Operations | Patching, upgrades, capacity, backups, troubleshooting, specialist skills |
| Delivery | Deployment model, automation, rollback, compatibility requirements |
| Cost | Baseline cost drivers, peak cost drivers, data-transfer implications |
| Sustainability | Idle capacity, elastic scaling, retention and resource efficiency |
| Lock-in/portability | Proprietary APIs/formats and practical migration path |

## Decision format

For each option:

**Why it fits**
- Concrete workload reasons.

**Why it may not fit**
- Concrete constraints or operational costs.

**Decision**
- Chosen / rejected / deferred.

**Validation**
- Benchmark, POC, load test, failure test, cost model, or security review.

**Revisit when**
- A measurable assumption changes.

## Anti-patterns

The following are not valid reasons by themselves:

- "Industry standard."
- "Cloud native."
- "Everyone uses Kubernetes."
- "Serverless scales automatically."
- "NoSQL is faster."
- "Kafka is more scalable."
- "Microservices are best practice."
- "Managed service means zero operations."

Every statement must be connected back to Veyra's workload.
