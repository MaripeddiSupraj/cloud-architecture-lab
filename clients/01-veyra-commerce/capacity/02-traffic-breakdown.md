# Traffic Breakdown Model

## Purpose

The platform-wide peak of 15,000 requests/second is not useful for sizing until it is decomposed by business capability. This model turns the aggregate peak into service-level load assumptions.

## Planning envelope

| Measure | Normal | Promotional peak |
|---|---:|---:|
| Total edge/API request rate | 700–1,200 RPS | 15,000 RPS |
| Orders/day | ~35,000 | up to ~150,000 |
| Registered customers | 2.5M | growth toward 5M |
| Monthly active customers | ~600k | campaign-dependent |

The 15k RPS number represents a short sale-event stress envelope, not sustained all-day demand.

## Peak request distribution assumption

Until production telemetry exists, use this distribution for load tests:

| Capability | Share | Peak RPS |
|---|---:|---:|
| Product browse/detail | 40% | 6,000 |
| Search/filter | 20% | 3,000 |
| Customer/session/profile | 8% | 1,200 |
| Cart | 10% | 1,500 |
| Pricing/promotion evaluation | 8% | 1,200 |
| Checkout/order APIs | 5% | 750 |
| Inventory availability | 5% | 750 |
| Other/admin/supporting | 4% | 600 |
| **Total** | **100%** | **15,000** |

This is deliberately conservative for checkout APIs. Actual completed-order TPS is much lower than checkout API RPS because a checkout journey makes multiple API calls and many sessions do not convert.

## Completed order burst model

Daily averages are misleading.

For a 150,000-order campaign day, model these concentration scenarios:

| Scenario | Orders in window | Average order commits/sec |
|---|---:|---:|
| 20% of daily orders in 60 min | 30,000 | 8.3 |
| 20% in 15 min | 30,000 | 33.3 |
| 10% in 5 min | 15,000 | 50.0 |
| Stress test | — | **75 commits/sec** |

The authoritative order/inventory path must be tested at **75 committed orders/sec** with additional failed/retried attempts around it.

## Cacheability assumptions

| Traffic class | Initial cacheability assumption |
|---|---:|
| Product media/static web assets | 85–95% edge hit ratio target |
| Product detail payload fragments | 60–80% application-cache hit target where personalization does not invalidate caching |
| Search results | query-dependent; do not assume a global hit ratio |
| Pricing/promotion | selective only; checkout price always authoritative |
| Cart/profile/checkout | personalized; not edge-cacheable |

## External dependency envelope

Payment/provider integrations must be tested independently from customer RPS.

Initial checkout provider stress:
- 75 payment-create attempts/sec,
- callback bursts above request rate,
- provider P95 latency simulation at 300 ms / 1 s / 3 s,
- controlled timeout scenarios at 5–10 seconds,
- duplicate callback delivery,
- provider rate-limit response.

## Validation status

**Planning model only.**

Replace assumptions with:
- load-test results,
- production access logs,
- CloudFront/ALB request distributions,
- order funnel telemetry,
- payment-provider measured limits.

The architecture must be revisited if the real traffic shape differs materially from this model.
