# ECS Service Sizing Model

## Purpose

Translate service-level RPS into an initial ECS/Fargate task envelope. These are **load-test starting points**, not final production reservations.

## Sizing principle

For request-serving services:

```
required_tasks =
  ceil(peak_service_rps / validated_rps_per_task)
  × safety_factor
```

Use a 1.25–1.50 safety factor for sale-event planning depending on startup time and downstream headroom.

Production minimums span multiple Availability Zones even when normal traffic could run on fewer tasks.

## Initial benchmark profiles

Start benchmark testing with these task sizes:

| Service | Starting task size | Benchmark target |
|---|---|---:|
| Experience API | 1 vCPU / 2 GiB | 250 RPS/task |
| Catalog/Pricing | 1 vCPU / 2 GiB | 200 RPS/task |
| Cart | 0.5 vCPU / 1 GiB | 150 RPS/task |
| Checkout/Orders | 1 vCPU / 2 GiB | 75 RPS/task |
| Inventory | 1 vCPU / 2 GiB | 100 RPS/task |
| Search index worker | 1 vCPU / 2 GiB | backlog/throughput driven |
| Integration worker | 0.5 vCPU / 1 GiB | backlog/latency driven |

These targets assume healthy downstream systems and must be replaced with actual benchmark measurements.

## Peak task envelope

Using the current traffic model and ~30% headroom:

| Service | Peak service RPS | RPS/task assumption | Calculated | Planned peak |
|---|---:|---:|---:|---:|
| Experience API | 15,000 | 250 | 60 | **78** |
| Catalog/Pricing | 7,200* | 200 | 36 | **48** |
| Cart | 1,500 | 150 | 10 | **15** |
| Checkout/Orders | 750 API RPS | 75 | 10 | **15** |
| Inventory | 750 availability + reservation calls | 100 | 8 | **12** |

\* Browse + pricing paths overlap; detailed tracing will refine the exact internal call rate.

This is intentionally a high ceiling for stress testing. Production auto-scaling maximums may be lower if downstream database/search/provider limits are reached first.

## Normal production minimum

Initial minimum task counts:

| Service | Min tasks | Reason |
|---|---:|---|
| Experience API | 6 | normal traffic + multi-AZ headroom |
| Catalog/Pricing | 6 | browse-heavy path |
| Cart | 3 | one-per-AZ baseline |
| Checkout/Orders | 3 | one-per-AZ critical path |
| Inventory | 3 | one-per-AZ critical path |
| Search worker | 2 | asynchronous; not required one-per-AZ at all times |
| Integration worker | 3 | provider/fulfilment continuity |

Baseline: **26 tasks** before event pre-scaling.

## Promotional pre-scaling target

Do not wait for CPU alarms to grow from 26 tasks to full peak.

Before a known major sale:
- Experience: 30–40 tasks,
- Catalog: 20–25,
- Cart: 6–9,
- Checkout: 6–9,
- Inventory: 6–9,
- workers: scale from known backlog/throughput requirement.

Reactive target tracking then grows toward the tested maximum.

## Scaling metrics

| Workload | Primary scaling signal |
|---|---|
| Experience/Catalog/Cart | ALB request count per target or CPU after correlation testing |
| Checkout/Inventory | request rate + CPU + downstream saturation guardrails |
| Search workers | queue depth / oldest message age / processing rate |
| Integration workers | queue age + provider concurrency/rate limits |

CPU alone is not accepted as the universal scaling signal.

## Startup-time requirement

Measure:
- image pull time,
- application initialization,
- JVM warmup where applicable,
- health-check grace period,
- time from scale request to healthy ALB target.

For sudden sale events, the time-to-healthy metric is as important as theoretical max task count.

## ARM/Graviton test

Fargate supports Linux/ARM pricing. Benchmark Java/Node containers on ARM and x86 using the same workload.

Adopt ARM only when:
- application dependencies support it,
- latency/throughput are equal or better,
- operational tooling remains compatible.

AWS states Fargate pricing is based on requested vCPU, memory, OS, architecture and storage, and Fargate Savings Plans can reduce stable compute usage; therefore architecture/runtime sizing must be measured rather than guessed.

## Validation gate

ADR-002 remains accepted only if representative load testing proves:
- per-task throughput assumptions,
- scale-out responsiveness,
- acceptable cost at baseline,
- no downstream saturation before service maximums.
