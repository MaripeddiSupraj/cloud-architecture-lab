# Initial Capacity Model

## Purpose

This model converts business volume into engineering planning assumptions. It is intentionally independent of cloud products and instance sizes.

## Traffic assumptions

Current planning envelope:

| Measure | Normal | Promotional peak |
|---|---:|---:|
| Customer/API request rate | 700–1,200 RPS | up to 15,000 RPS |
| Orders/day | ~35,000 | up to ~150,000 |
| Registered customers | 2.5M | growth toward 5M |
| Daily active customers | ~120k | event-dependent |

The peak request number is treated as a design test, not a permanently provisioned baseline.

## Workload shape

The platform is expected to be read-dominant during browse activity and transaction-dominant during checkout.

Initial classification:

- Catalog browse: very high reads, low write frequency.
- Search/filter: high reads, index-backed query pattern.
- Cart: high read/write frequency, short-lived customer state.
- Inventory: high read frequency with correctness-sensitive writes.
- Checkout/order: lower volume than browse, high correctness requirement.
- Payment integration: externally dependent, latency and failure sensitive.
- Notifications: asynchronous and non-blocking for order commit.
- Analytics: asynchronous and isolated from customer transaction paths.

## Order throughput sanity check

150,000 orders/day averages only ~1.74 orders/second across 24 hours. That average is misleading for promotional events.

For design purposes, order capacity will use burst windows rather than daily averages. A later model will define event assumptions such as percentage of daily orders concentrated within 5, 15, and 60 minute windows.

## Data growth questions to resolve

Before storage sizing is finalized, discovery must confirm:

- Average product document size and attribute count.
- Product image count and average encoded size.
- Average order payload size and line-item count.
- Retention policy for orders, carts, events, logs, and audit records.
- Search-index expansion factor.
- Replication and backup retention requirements.
- Event payload sizes and retention windows.
- Customer profile and address cardinality.
- Expected media growth rate.

## Capacity model outputs required before Gate A closes

1. Request distribution by business capability.
2. Read/write ratios by data domain.
3. Peak checkout and order transaction rates.
4. Database transaction and connection estimates.
5. Cacheable versus non-cacheable traffic.
6. Search QPS and index-growth estimate.
7. Event throughput and retention estimate.
8. Internet and inter-service data-transfer estimates.
9. Log/metric/trace ingestion estimates.
10. Three-year storage growth range.

These outputs will be used to evaluate architecture options rather than to retroactively justify a preferred service.
