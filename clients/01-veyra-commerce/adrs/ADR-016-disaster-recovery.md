# ADR-016 — Regional disaster recovery strategy

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Platform / Business Continuity

## Context

Initial critical-data targets are RPO <= 5 minutes and RTO <= 30 minutes. Multi-AZ protects against Availability Zone failures but not a full Regional outage.

The primary market is India, and keeping primary and DR Regions in India simplifies the initial residency posture while providing Regional separation.

## Region selection

- **Primary:** Asia Pacific (Mumbai) — ap-south-1
- **DR:** Asia Pacific (Hyderabad) — ap-south-2

## Options considered

### Backup and restore only

**Why credible**
- Lowest standby cost.

**Why not selected for critical commerce data**
- Restoring database, platform, images, secrets and routing from cold backup is unlikely to reliably meet a 30-minute RTO.

### Full active-active multi-Region

**Why credible**
- Can reduce Regional recovery time and provide regional serving capability.

**Why not selected**
- Orders, inventory and payment workflows introduce significant cross-Region consistency/conflict complexity.
- Doubles or materially increases steady-state platform cost.
- No launch requirement currently needs active-active customer writes.

### Pilot light / warm data standby

**Why it fits**
- Critical recovery prerequisites exist before the incident.
- Application capacity need not run at full production scale continuously.
- Supports a controlled, testable failover process.

## Decision

Use a **pilot-light architecture with warm replicated data prerequisites**:

- production infrastructure definitions exist for both Regions,
- VPC/network/ALB/IAM/recovery controls are pre-provisioned in DR where required for RTO,
- RDS PostgreSQL uses a cross-Region read replica in Hyderabad for critical transactional data,
- container images replicate to DR ECR,
- required secrets replicate to the DR Region,
- critical S3 data uses an appropriate combination of versioning, backup, and cross-Region replication/recovery copies,
- application services remain at minimal or zero standby capacity when safe and scale during declared failover,
- Route 53/CloudFront origin/routing changes are automated through a controlled DR runbook,
- failover remains a human-authorized business continuity action rather than an uncontrolled automatic split-brain event.

## Database failover

The DR runbook verifies replication health/lag, promotes the cross-Region PostgreSQL replica, updates regional connection configuration, then starts/scales application workloads.

The target is an **engineering objective**, not an assumption: quarterly DR exercises must prove the actual RPO/RTO.

## Why not rely only on Multi-AZ?

Multi-AZ addresses in-Region AZ/instance failure. It is not a Region-level DR strategy.

## Failback

Failback is a separate controlled migration after the original Region is healthy. The system does not automatically "flip back" merely because a health check recovers.

## Well-Architected impact

- **Operational Excellence:** rehearsed runbook and measurable recovery.
- **Security:** DR uses replicated controlled secrets/roles rather than emergency static credentials.
- **Reliability:** Region failure has a prebuilt recovery path.
- **Performance Efficiency:** DR application compute scales only when required.
- **Cost Optimization:** avoids always-running full active-active production.
- **Sustainability:** minimal standby compute instead of duplicated peak infrastructure.

## Validation

At least quarterly:
- simulate primary Region unavailability,
- record last committed primary transaction and replica promotion point,
- start/scale DR application,
- verify payment callbacks/integrations in DR,
- verify customer routing,
- measure achieved RPO/RTO,
- perform restoration tests independent of replication.

## Diagram

[Disaster recovery](../diagrams/08-disaster-recovery.eraserdiagram)

## References

- RDS PostgreSQL cross-Region read replicas: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.XRgn.html
- ECR replication: https://docs.aws.amazon.com/AmazonECR/latest/userguide/replication.html
- Secrets Manager regional replication: https://docs.aws.amazon.com/secretsmanager/latest/userguide/replicate-secrets.html
