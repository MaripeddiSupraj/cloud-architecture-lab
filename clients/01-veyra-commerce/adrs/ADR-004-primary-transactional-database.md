# ADR-004 — Primary transactional database

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Commerce Engineering / Data

## Context

Orders, payment state, inventory reservations, customer addresses, and other transactional commerce data require durable state, constraints, multi-record transactions, and clear reconciliation. Peak order volume is materially lower than browse/search traffic and the team already has PostgreSQL experience.

## Options considered

### Amazon RDS for PostgreSQL — Multi-AZ

**Why it fits**
- Relational transactions and constraints match order/payment/inventory correctness requirements.
- PostgreSQL is already familiar to the engineering team.
- RDS removes host/database infrastructure management while supporting backups, point-in-time recovery, and high-availability Multi-AZ deployments.
- A Multi-AZ DB instance provides a synchronous standby in another Availability Zone for failover.

**Trade-offs**
- Writer scaling remains primarily vertical; schema/query discipline and connection management still matter.
- Multi-AZ increases cost compared with Single-AZ.
- Applications must tolerate brief connection interruption during failover.

### Amazon Aurora PostgreSQL-Compatible

**Why credible**
- Strong option when the workload requires Aurora-specific scaling, replica, recovery, or global database capabilities.

**Why not selected initially**
- Current transactional volume does not demonstrate a requirement that RDS PostgreSQL Multi-AZ cannot meet.
- Selecting Aurora before measuring database load would pay for additional platform capability without proven need.

### Amazon DynamoDB

**Why credible**
- Excellent fit for known access patterns requiring very high horizontal scale and low operational overhead.

**Why not selected for core order data**
- Core commerce transactions have relational entities, constraints, reconciliation queries, and multi-record workflow state.
- The current team already operates effectively with PostgreSQL.
- Using NoSQL solely for theoretical scale would shift complexity into application-level modelling without evidence that relational scaling is the bottleneck.

DynamoDB may still be selected later for a workload whose access pattern specifically benefits from it.

### Self-managed PostgreSQL on EC2/ECS

**Why not selected**
- Database host patching, failover automation, backup engineering, and recovery operations are undifferentiated work for this team.

## Decision

Use **Amazon RDS for PostgreSQL in Multi-AZ configuration** as the initial authoritative transactional database platform.

Logical ownership and schema boundaries will follow domain ownership. This ADR does not require a separate physical database instance for every service.

## Well-Architected impact

- **Operational Excellence:** managed backups, maintenance, metrics, and failover reduce database operations.
- **Security:** private network placement, encryption, IAM-integrated operational controls, and managed credentials can be applied.
- **Reliability:** Multi-AZ standby provides AZ-level database failover protection.
- **Performance Efficiency:** relational indexing/query tuning is appropriate to current workload; browse/search traffic is kept off the primary transaction path.
- **Cost Optimization:** avoids adopting a more expensive architecture before a measured need exists.
- **Sustainability:** right-sized managed instances avoid unnecessary database fleets.

## Validation

- Model peak checkout/order TPS rather than daily averages.
- Load test representative transactional queries and connection counts.
- Perform a Multi-AZ failover exercise and measure application recovery.
- Validate backup restore and point-in-time recovery.

## Revisit triggers

- Sustained writer/reader demand exceeds practical RDS scaling.
- Failover/recovery objectives require capabilities better met by Aurora.
- Cross-region active data requirements become mandatory.
- A domain develops non-relational access patterns at materially different scale.

## Reference

Amazon RDS Multi-AZ: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html
