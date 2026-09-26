# ADR-008 — Product and static media storage

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Catalog Engineering / Platform

## Context

Veyra has roughly 8 TB of product/media content and needs durable storage for product images, static web assets, exports, and similar objects. These objects are read far more often than they are changed and should be delivered through the edge rather than application containers.

## Options considered

### Amazon S3

**Why it fits**
- Object storage matches immutable/versioned media and static asset access patterns.
- Integrates naturally with CloudFront for edge delivery.
- Removes filesystem server management.
- Lifecycle/versioning policies can manage retention and recovery.

**Trade-offs**
- Applications must use object semantics rather than a mounted POSIX filesystem.
- Public access controls and origin access must be configured carefully.

### Amazon EFS

**Why credible**
- Appropriate when applications require a shared POSIX filesystem mounted concurrently by compute.

**Why not selected**
- Product media does not require filesystem locking or mounted shared storage.
- Serving customer media from a shared filesystem would add unnecessary filesystem capacity/performance management.

### Store binaries in PostgreSQL

**Why not selected**
- Large object/media traffic would consume transactional database storage, backup, I/O, and restore capacity without providing transactional value.

### Local ECS task storage

**Why not selected**
- Task storage is ephemeral and tied to individual runtime instances; it is not a durable shared media repository.

## Decision

Use **Amazon S3** as the durable object store for product media and static assets, with **CloudFront** as the customer delivery path.

S3 buckets must remain non-public unless a specific exception is reviewed. Edge-origin access, versioning/lifecycle, encryption, retention, and backup requirements will be defined in the security/data design.

## Well-Architected impact

- **Operational Excellence:** no media filesystem servers to operate.
- **Security:** private object storage with controlled application/edge access.
- **Reliability:** durable object storage is independent of application task lifecycle.
- **Performance Efficiency:** CloudFront absorbs repeated customer reads close to users.
- **Cost Optimization:** storage tiers/lifecycle and cache hit ratio can control storage and transfer cost.
- **Sustainability:** lifecycle policy can remove or tier unused object versions instead of retaining hot capacity indefinitely.

## Validation

- Model image count, size distribution, request volume, and cache hit ratio.
- Test upload/versioning workflows.
- Verify direct public bucket access is blocked.
- Define lifecycle behavior for obsolete media versions.
