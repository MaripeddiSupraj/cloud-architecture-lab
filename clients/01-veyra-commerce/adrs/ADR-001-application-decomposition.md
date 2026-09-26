# ADR-001 — Application decomposition strategy

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Commerce Engineering

## Context

Veyra has a six-month launch target, approximately 18 backend engineers, four platform engineers, and several commerce domains with different consistency and failure characteristics.

The architecture needs domain isolation without creating dozens of independently operated services before the organization has a reason to carry that complexity.

## Decision drivers

- Protect checkout/order/inventory correctness.
- Allow high-read browse/search workloads to scale independently from transactional paths.
- Limit blast radius of external payment and notification failures.
- Fit the operating capacity of the existing team.
- Preserve a path to further decomposition based on measured pressure.

## Options considered

### Single monolith

**Why it fits**
- Simplest deployment and local development.
- Lowest distributed-systems overhead.

**Why not selected**
- Search/index processing, customer browse, transactional checkout, and external integrations have materially different scaling and failure characteristics.
- A single deployable would create a larger blast radius than necessary.

### Fine-grained microservices

**Why it fits**
- Maximum independent ownership, deployment, and scaling.

**Why not selected**
- The current team and launch horizon do not justify dozens of services, databases, pipelines, dashboards, on-call surfaces, network paths, and compatibility contracts.
- Distribution would introduce failure modes before there is evidence those boundaries are needed.

### Coarse-grained domain services

**Why it fits**
- Creates independent scaling/failure boundaries for the workloads that genuinely differ.
- Keeps the initial service count understandable.
- Allows internal modules to remain strongly cohesive.
- Provides an evidence-based path to split hot modules later.

## Decision

Adopt a **coarse-grained domain-service architecture**.

Initial logical deployment boundaries:

1. Experience/Commerce API
2. Catalog & Pricing
3. Cart
4. Checkout & Orders
5. Inventory
6. Search/Indexing
7. Integration & Async Workers

Notifications and low-value background functions may initially remain worker modules rather than independent network services.

Service boundaries are not permission to create a database per service automatically; data ownership is decided separately.

## Well-Architected impact

- **Operational Excellence:** bounded service count keeps deployments and on-call manageable.
- **Security:** fewer network identities and trust relationships at launch.
- **Reliability:** critical transactional paths can be isolated from search/notification failures.
- **Performance Efficiency:** read-heavy and transaction-heavy components can scale independently.
- **Cost Optimization:** avoids paying the fixed operational/capacity overhead of excessive services.
- **Sustainability:** avoids unnecessary always-on duplicate runtime capacity.

## Validation

- Map all critical customer flows across the proposed boundaries.
- Run peak-load tests with browse/search separated from checkout.
- Verify deployment ownership remains supportable by current teams.

## Revisit triggers

Split a module further when at least one is demonstrated:
- independent scaling pressure,
- repeated release coordination,
- unacceptable failure blast radius,
- separate security boundary,
- team ownership conflict,
- incompatible runtime/resource requirements.
