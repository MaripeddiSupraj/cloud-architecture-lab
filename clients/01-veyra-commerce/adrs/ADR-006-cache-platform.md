# ADR-006 — Distributed cache platform

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Platform

## Context

Catalog reads, pricing lookups, session/cart-adjacent ephemeral data, rate-limiting support, and other hot data can create avoidable repeated work during promotional traffic. Cache is an optimization and coordination mechanism, never the sole durable record for orders, payments, or inventory truth.

## Options considered

### Amazon ElastiCache Serverless for Valkey

**Why it fits**
- Managed in-memory cache with automatic capacity management matches highly variable normal-versus-sale traffic.
- Valkey provides familiar key/value, TTL, atomic operation, and data-structure semantics.
- Serverless reduces node sizing, replacement, patching, and cluster-capacity operations for the small platform team.

**Trade-offs**
- Pay-per-use economics must be compared with node-based caches for steady high utilization.
- Serverless provides less low-level configuration control.
- Cache misses and cache outages must be safe for authoritative flows.

### ElastiCache node-based Valkey

**Why credible**
- Better fine-grained topology/control and can become more economical for predictable sustained workloads.

**Why not selected initially**
- Current demand is intentionally bursty and capacity is not yet well characterized.
- Veyra should not take node/capacity planning work until measurements show a financial or technical reason.

### Application-local in-memory cache only

**Why credible**
- Lowest latency and no network dependency.

**Why not sufficient**
- ECS tasks scale horizontally, so local caches are inconsistent across instances and cannot provide shared ephemeral state.
- It can still be used as a small L1 cache for carefully selected immutable/short-lived values.

### Database as cache

**Why not selected**
- Repeated hot reads would consume the transactional database capacity that should remain available for correctness-sensitive operations.

## Decision

Use **Amazon ElastiCache Serverless for Valkey** as the initial distributed cache platform.

Cache-aside is the default read pattern. Every cached data class must define TTL, invalidation behavior, stampede protection where relevant, and behavior when cache is unavailable.

## Well-Architected impact

- **Operational Excellence:** eliminates cache-node fleet sizing/patching at launch.
- **Security:** cache remains private and authenticated/encrypted according to detailed design.
- **Reliability:** authoritative paths must tolerate cache loss; cache cannot be transaction truth.
- **Performance Efficiency:** hot reads avoid repetitive database/application work.
- **Cost Optimization:** elastic capacity matches bursty demand; benchmark against node-based Valkey as utilization stabilizes.
- **Sustainability:** capacity follows demand instead of permanently provisioning for sale peaks.

## Validation

- Measure hit ratio per cacheable data class.
- Load test cache-miss storms and cold start after cache loss.
- Validate application correctness with cache disabled.
- Compare serverless versus node-based monthly economics after traffic measurement.

## Reference

ElastiCache deployment options: https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/WhatIs.deployment.html
