# ADR-010 — Inventory reservation and oversell control

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Commerce Architecture / Inventory Engineering

## Context

Browse availability may tolerate slight staleness, but checkout reservation cannot use a stale cache value as proof that stock exists. Flash-sale SKUs can create very high contention on a small number of inventory records.

## Decision drivers

- Prevent confirmed reservations from exceeding sellable stock.
- Make customer retries idempotent.
- Keep the initial solution understandable and auditable.
- Do not use cache as inventory truth.
- Support expiration/release of abandoned reservations.
- Provide a path for hot-SKU scaling if measured contention becomes a problem.

## Options considered

### PostgreSQL atomic conditional reservation

A short transaction performs an atomic conditional update such as reducing available quantity only when sufficient stock remains, and writes a reservation record with a unique checkout/idempotency reference.

**Why it fits**
- Inventory/order transactional state already requires relational correctness.
- Database constraints and row-level concurrency provide a clear correctness boundary.
- Peak order TPS is much lower than browse RPS.
- The approach is easy to reconcile and audit.

**Trade-offs**
- Extremely hot individual SKUs can create row contention.
- Database write throughput remains a scaling boundary.

### Valkey/Redis atomic counter as source of truth

**Why credible**
- Very fast atomic operations and useful for high-contention counters.

**Why not selected as authoritative inventory**
- Cache loss/failover/eviction semantics must not decide whether physical stock exists.
- Reconciliation between cache and durable inventory would become the correctness problem.

Valkey may cache availability views but not authorize a sale.

### DynamoDB conditional writes

**Why credible**
- Strong conditional-write semantics and horizontal scaling can suit very high-scale inventory keys.

**Why not selected initially**
- Current measured order throughput does not justify splitting inventory truth into a separate NoSQL model.
- Hot-key behaviour still requires capacity/model consideration.
- It adds another source-of-truth technology before PostgreSQL has been shown insufficient.

### Serialize every reservation through a queue

**Why credible**
- Can control contention for extreme flash-sale items.

**Why not selected globally**
- Adds latency and asynchronous complexity to normal checkout.
- Most SKUs do not require serialization.

A per-hot-SKU serialized admission pattern remains a targeted future option.

## Decision

Use **PostgreSQL as authoritative inventory reservation state** with:

- short transactions,
- atomic conditional decrement/update,
- unique idempotency/reservation keys,
- explicit reservation status,
- reservation expiry,
- idempotent release/commit,
- no external API calls inside the inventory transaction.

Browse availability may be cached/derived. Checkout always revalidates through the authoritative reservation path.

## Flash-sale guardrail

If load testing shows unacceptable contention for a small set of hot SKUs, introduce a **targeted hot-item admission/serialization mechanism** rather than redesigning every product around the exceptional case.

## Well-Architected impact

- **Operational Excellence:** one auditable inventory truth and clear reconciliation.
- **Security:** no additional public/data surface.
- **Reliability:** durable reservation survives task/cache failure.
- **Performance Efficiency:** normal products use fast short DB transactions; cache handles browse reads.
- **Cost Optimization:** avoids premature separate inventory datastore/streaming infrastructure.
- **Sustainability:** adds complexity only to measured hot paths.

## Validation

- Thousands of concurrent attempts against stock quantity 10.
- Duplicate checkout retries.
- Reservation expiration under worker failure.
- Task termination after decrement but before API response.
- Database failover during reservation.
- Hot-SKU lock/contention measurement.

## Revisit triggers

- Measured hot-item contention breaks checkout SLO.
- Inventory write throughput exceeds practical relational scaling.
- Inventory ownership moves to an external system of record.
