# Peak Event Readiness

## Design scenario

Normal platform traffic: ~700–1,200 RPS.  
Architecture stress target: up to ~15,000 RPS during major promotional events.

The goal is not simply to make ECS tasks reach 15k RPS. Every shared dependency and external provider must remain inside a safe operating envelope.

## Test stages

### 1. Baseline
Validate normal-day latency/resource profile and cache hit ratio.

### 2. Ramp
Increase gradually to identify the first bottleneck and scaling correlation.

### 3. Sudden step
Simulate campaign traffic jumping faster than reactive scaling.

### 4. Sustained peak
Hold peak long enough to expose:
- memory leaks,
- DB pool exhaustion,
- queue growth,
- search/cache saturation,
- provider rate limits,
- telemetry ingestion effects.

### 5. Flash-sale hot SKU
Concentrate a large share of checkout requests on stock quantity far below demand and prove no oversell.

### 6. Dependency degradation
At peak load inject:
- payment latency/timeouts,
- cache loss,
- OpenSearch degradation,
- slow ERP,
- failed notification provider.

Non-critical failures must not collapse checkout.

## Promotion-event runbook

Before event:
- verify recent production deployment stability,
- freeze risky schema/destructive changes,
- pre-scale selected ECS services,
- confirm database connection headroom,
- warm critical caches where safe,
- confirm search indexing health,
- confirm payment/provider quotas,
- inspect DLQs/backlog,
- verify on-call ownership and rollback command paths.

During event:
- watch customer/business SLIs first,
- protect checkout with rate/backpressure controls before database exhaustion,
- do not respond to every CPU spike by blindly raising max capacity,
- watch provider timeouts and payment UNKNOWN backlog.

After event:
- scale back deliberately,
- review queue drain,
- reconcile payments/inventory,
- compare forecast versus actual spend/load,
- update capacity model with observed values.

## Pass criteria

The event is considered architecture-safe only when:
- customer latency/error SLOs hold or degrade within agreed policy,
- no oversell occurs,
- no duplicate order/charge occurs,
- DB connections remain below safe ceiling,
- async backlog drains within SLO,
- recovery from one-AZ task loss remains acceptable,
- cost per transaction does not show unexplained step-change.
