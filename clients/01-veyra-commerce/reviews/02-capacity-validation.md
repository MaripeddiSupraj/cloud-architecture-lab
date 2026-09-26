# Capacity Validation Review

**Status:** Planning model complete; benchmark evidence pending.

## What the model shows

The architecture's main scaling problem is **read/request traffic**, not order commits.

- 15k peak customer/API RPS can require tens of ECS tasks.
- Completed-order stress is modelled at ~75 commits/sec.
- PostgreSQL therefore should not receive 15k RPS directly.
- CloudFront, Valkey and OpenSearch are architectural offload layers, not decorative services.
- ECS auto scaling must respect database/provider limits.
- Connection budgets are a first-class database constraint.

## Current estimated task envelope

Normal production:
- ~26 always-on tasks across request services/workers.

Stress maximum:
- Experience: ~78
- Catalog/Pricing: ~48
- Cart: ~15
- Checkout/Orders: ~15
- Inventory: ~12
- workers: backlog-dependent

This does **not** mean all ~168 tasks run for a whole month. The event maximum is a short-duration ceiling.

## Architecture decisions strengthened by this model

### CloudFront remains justified
Media/static traffic can be tens of TB/month and should not consume ECS origin capacity repeatedly.

### OpenSearch remains justified
3k peak search RPS should not compete with PostgreSQL OLTP.

### Valkey remains justified
Hot catalog/cart-adjacent reads can be absorbed without turning RDS into the read cache.

### SQS/EventBridge remain justified
Background work can scale from backlog independently of request services.

### ECS/Fargate remains plausible
The normal baseline is moderate and the sale-event capacity is highly elastic.

### RDS PostgreSQL remains plausible
The transactional stress envelope is orders of magnitude below aggregate browse RPS, provided cache/search separation and connection discipline hold.

## Main architecture risk exposed

**Connection amplification.**

Horizontal ECS scale can overwhelm PostgreSQL through connection count before CPU reaches its limit.

Action:
- enforce small connection pools,
- benchmark failover/reconnect storms,
- introduce RDS Proxy only if measurements justify it.

## Evidence still required

Before production sizing is approved:
- RPS/task benchmark for each synchronous service,
- task startup time,
- DB TPS/latency/connection benchmark,
- inventory hot-key contention test,
- cache hit ratio,
- OpenSearch query benchmark,
- payment-provider quota/latency limits,
- log/trace volume measurement.

## Gate decision

The architecture remains valid for implementation prototyping, but **capacity sizes are not production-approved until benchmark evidence replaces the current assumptions**.
