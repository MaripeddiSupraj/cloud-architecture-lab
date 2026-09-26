# ADR-000 — AWS implementation baseline

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture

## Context

The architecture lab needs one concrete provider implementation for Client 01 so that service-level decisions, operational design, pricing, failure modes, and deployment patterns can be evaluated rather than remaining generic.

This ADR does **not** claim that AWS is universally better than Azure or Google Cloud. AWS is a scenario constraint for this engagement.

## Decision drivers

- Use one provider deeply enough to make realistic service and cost decisions.
- Apply a mature architecture review framework consistently.
- Prefer managed capabilities where they reduce operational work.
- Keep architecture principles portable even when the implementation uses provider-specific services.

## Options considered

### AWS

**Why it fits**
- Provides the managed compute, relational database, cache, search, queue/event, edge, identity, observability, and security capabilities needed by the workload.
- The AWS Well-Architected Framework provides the six-pillar review model used by this engagement.
- India-region deployment is available.

**Trade-offs**
- Individual managed-service choices can create AWS-specific operational and API dependencies.
- Multi-cloud portability is not automatic.

### Azure

**Why credible**
- Equivalent managed capabilities exist for this workload.
- Would be appropriate where enterprise Microsoft/Azure platform strategy is already a constraint.

**Why not selected here**
- Running a second provider evaluation would add breadth but not improve the specific learning objective of taking one architecture through detailed implementation.

### Google Cloud

**Why credible**
- Strong managed compute, data, networking, and operational services are available.

**Why not selected here**
- Same reason as Azure: the engagement benefits more from depth in a single implementation than superficial multi-cloud duplication.

### Multi-cloud implementation

**Why not selected**
- No business requirement currently justifies duplicated platform engineering, security, networking, observability, delivery, and DR mechanisms across providers.

## Decision

Use AWS as the Client 01 implementation baseline. The primary region will be an India AWS Region; exact primary/DR region selection remains a later availability and data-residency decision.

## Well-Architected impact

- **Operational Excellence:** one platform reduces operational surface area.
- **Security:** one identity/control plane simplifies governance.
- **Reliability:** provider-native HA/DR capabilities can be evaluated concretely.
- **Performance Efficiency:** service sizing can be based on actual AWS scaling models.
- **Cost Optimization:** pricing can be modelled rather than estimated generically.
- **Sustainability:** elastic managed services can be evaluated against always-on capacity.

## Revisit triggers

- Contractual multi-cloud requirement.
- Material acquisition/enterprise platform mandate.
- A mandatory capability cannot satisfy workload requirements on AWS.
- Data sovereignty requirements force a different provider/location.
