# ADR-019 — Scaling and peak-event capacity strategy

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Performance Engineering / Platform / Architecture

## Context

Normal customer traffic is expected around 700–1,200 RPS while large promotional events may approach 15,000 RPS. Scaling only after CPU becomes critical can be too slow for sudden event ramps.

## Decision

Use a layered scaling model.

### Request-serving ECS services
- ECS Service Auto Scaling with target tracking.
- Select the scaling metric per service using load-test correlation: CPU, memory, or ALB request count per target.
- Maintain a non-zero production minimum across multiple AZs.
- Define a tested maximum task count based on downstream database/cache/provider capacity.

### Queue workers
Scale from backlog pressure, using queue depth/oldest-message age and processing-rate-derived metrics rather than request-serving CPU alone.

### Known promotional events
Use **scheduled/pre-scaling** to create headroom before announced sale windows. Reactive auto scaling remains active for unexpected demand beyond forecast.

### Faster signals
Use higher-resolution ECS scaling metrics only where load tests show the default control loop reacts too slowly; faster telemetry carries additional cost and should solve a measured problem.

### Database
- Protect with connection budgets and application pooling.
- Load test scale-out connection storms.
- Evaluate RDS Proxy if application task scaling/failover causes harmful connection churn or database connection pressure.
- Scale database compute/storage based on measured OLTP limits, not web RPS.

### Search/cache
Scale independently from order transactions. Search and cache failure must not corrupt authoritative commerce state.

## Why not only scheduled scaling?

Unexpected traffic, slow campaigns, and organic growth do not follow calendars.

## Why not only reactive scaling?

A sale can jump traffic faster than new tasks warm and downstream systems stabilize.

## Why not provision permanently for 15k RPS?

It would optimize rare events at the expense of normal-day cost and sustainability.

## Load-test gates

Before major sale events validate:
- cold-to-peak task scale-out time,
- P95/P99 latency,
- database connection utilization,
- inventory hot-SKU contention,
- cache hit ratio/cold-cache behavior,
- OpenSearch saturation,
- external provider timeout/rate limits,
- queue backlog drain time.

## Well-Architected impact

- **Operational Excellence:** scaling limits and event runbooks are explicit.
- **Security:** max capacity/rate limits help contain abusive demand.
- **Reliability:** known events are pre-scaled and downstream limits are respected.
- **Performance Efficiency:** each service scales on a signal correlated to its work.
- **Cost Optimization:** baseline follows normal demand rather than sale maximum.
- **Sustainability:** reduces idle peak capacity.

## References

- ECS Service Auto Scaling: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html
- Target tracking: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/target-tracking-create-policy.html
