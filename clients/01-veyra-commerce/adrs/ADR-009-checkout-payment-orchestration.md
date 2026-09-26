# ADR-009 — Checkout and payment orchestration

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Commerce Architecture / Checkout Engineering

## Context

Checkout crosses inventory reservation, order creation, an external payment provider, and asynchronous fulfilment/notification work. A single ACID transaction cannot span all of these systems.

The critical failure case is not simply "payment failed." It is **payment outcome unknown**: the provider may have charged the customer while Veyra lost the HTTP response or callback.

## Decision drivers

- Never intentionally create duplicate orders or charges for a customer retry.
- Persist enough state to recover after process/container failure.
- Avoid holding database transactions open across external payment calls.
- Support asynchronous provider callbacks and reconciliation.
- Keep order truth inside the commerce domain.
- Make downstream fulfilment/notification failure independent from payment commit.

## Options considered

### Application-owned durable state machine + PostgreSQL + transactional outbox

**Why it fits**
- Order/payment state remains in the authoritative commerce database.
- State transitions can use relational transactions and constraints.
- Idempotency keys map retries to an existing checkout attempt.
- The external payment call occurs outside the DB transaction.
- Provider callbacks and reconciliation workers can apply idempotent state transitions.
- The outbox commits the domain event in the same transaction as final order/payment state.

**Trade-offs**
- The checkout service owns explicit workflow/state-machine code.
- Reconciliation and timeout handling must be designed and tested carefully.
- Long-running workflow visibility must be built into observability.

### AWS Step Functions orchestration

**Why credible**
- Provides durable orchestration, retries, timeouts, execution history, and visual workflow state.

**Why not selected initially**
- The most critical state transitions are already tightly coupled to order/payment relational invariants.
- Moving orchestration state into a second authoritative workflow system would add coordination and operational concepts before workflow complexity requires it.
- The current flow is small enough to be explicit in the checkout domain.

Step Functions remains valid if the workflow grows into many long-running external stages with materially greater orchestration complexity.

### Distributed transaction / two-phase commit

**Why not selected**
- The external payment provider and most downstream systems do not participate in Veyra's database transaction.
- It would increase coupling and still not eliminate uncertain network outcomes.

### Synchronous chain: Checkout -> Payment -> Inventory -> Fulfilment -> Notification

**Why not selected**
- Customer success would depend on several unrelated external/downstream systems being healthy at the same time.
- Retries would be dangerous without durable idempotent state.

## Decision

Use an **application-owned checkout state machine** persisted in PostgreSQL.

Required controls:

1. Client/API supplies or receives an idempotency key for checkout/payment creation.
2. Order/payment attempt is persisted before calling the payment provider.
3. No database transaction is held open while waiting for the provider.
4. Provider merchant reference is unique and stable.
5. Payment callbacks are deduplicated.
6. Unknown/timeout outcomes transition to an explicit UNKNOWN/PENDING_CONFIRMATION state.
7. A reconciliation worker queries the provider for stale uncertain payments.
8. Final state change and outbox event are committed atomically.
9. Fulfilment, notifications, search/analytics updates remain asynchronous.

## Well-Architected impact

- **Operational Excellence:** explicit states make stuck payments and reconciliation visible.
- **Security:** raw card data stays outside Veyra; only provider references/tokens are stored.
- **Reliability:** process/network failure does not erase workflow state.
- **Performance Efficiency:** external wait time does not hold database locks.
- **Cost Optimization:** uses existing application/database/event components instead of a new orchestration service at current complexity.
- **Sustainability:** avoids duplicated workflow infrastructure without requirement.

## Validation

- Drop the payment HTTP response after provider authorization.
- Deliver the same provider callback repeatedly.
- Retry checkout with the same idempotency key.
- Kill the checkout task at every state transition.
- Delay provider response beyond application timeout.
- Verify a paid order emits the fulfilment event exactly once logically despite at-least-once delivery.

## Revisit triggers

- Checkout adds many long-running external workflow stages.
- Operational visibility of code-owned workflow becomes insufficient.
- Business workflows require configurable orchestration independent of application releases.

## Diagram

[Checkout/payment sequence](../diagrams/03-checkout-payment.eraserdiagram)  
[Payment uncertainty recovery](../diagrams/04-payment-uncertainty-recovery.eraserdiagram)
