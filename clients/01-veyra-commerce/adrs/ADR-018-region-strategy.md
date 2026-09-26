# ADR-018 — Region and geographic expansion strategy

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Product / Security

## Context

Veyra launches in India with potential Southeast Asia expansion. Launch architecture must not pay active-active complexity now merely because expansion is possible later.

## Decision

### Launch
- Primary Region: Mumbai (ap-south-1).
- DR Region: Hyderabad (ap-south-2).
- CloudFront provides global edge delivery for cacheable content.
- Transactional writes remain single-primary-Region.

### Expansion
Before launching transactional workloads in another geography, create a separate decision based on:
- user latency,
- data residency,
- payment/legal constraints,
- read/write locality,
- inventory/order ownership,
- cross-region consistency requirements,
- operational staffing,
- measured revenue versus platform cost.

## Why not active-active now?

Multi-Region writes create non-trivial order, inventory and payment conflict semantics. "Global" is not a reason to add those problems before the business requires them.

## Why CloudFront now?

Static/media delivery and edge security benefit immediately without moving transactional state closer to every user.

## Well-Architected impact

- **Operational Excellence:** one write region is simpler to operate.
- **Security:** data residency remains understandable at launch.
- **Reliability:** DR is separated from active-active application semantics.
- **Performance Efficiency:** edge delivery handles globally cacheable content.
- **Cost Optimization:** no full duplicate active production region.
- **Sustainability:** compute/data systems are added only when regional demand exists.

## Revisit triggers

- Southeast Asia transaction volume/latency makes regional compute economically justified.
- Regulatory residency requirement.
- Business requires regional autonomy.
- RTO requirement becomes too aggressive for pilot-light promotion.
