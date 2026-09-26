# Cost Optimization Decisions

## Principle

Cost optimization is not "choose the cheapest service." It is minimizing total cost while meeting reliability, security, performance, and operational requirements.

## Decisions already reducing cost

### ECS/Fargate instead of a permanently peak-sized EC2 cluster
Normal capacity is far below sale-event capacity. Elastic task scaling avoids carrying the entire event ceiling continuously.

### No EKS platform at launch
Avoids Kubernetes control/add-on/upgrade/on-call overhead where there is no Kubernetes-specific requirement.

### No Kafka/MSK at launch
SQS/EventBridge satisfy current buffering/routing requirements without a permanently running streaming platform.

### No active-active second Region
DR uses a pilot-light/warm-data model rather than continuously duplicating full production compute.

### No Network Firewall by default
WAF, security groups, routes and IAM satisfy current requirements. Add centralized inspection only when threat/compliance evidence requires it.

### One relational platform rather than database-per-service
Logical ownership comes before physical database sprawl.

### Risk-based deployment strategy
Blue/green/canary for high-risk customer transaction services; rolling deployment for lower-risk workers where duplicate capacity has little risk benefit.

## Optimization candidates after measurements

### Fargate ARM
Benchmark Graviton/ARM images. Adopt if application compatibility and price-performance are better.

### Compute Savings Plans
Commit only the stable baseline after at least several weeks of representative production utilization.

Do not commit sale-event peak capacity.

### Fargate Spot
Use only for interruption-tolerant asynchronous workers where SQS safely preserves work.

Do not use Spot as the only capacity for checkout/order/inventory request paths.

### ECS on EC2
Re-open ADR-002 if sustained baseline usage becomes large/stable enough that EC2 fleet economics outweigh host operations.

### Valkey node-based
Move from serverless only when working set/request rate is stable enough to demonstrate lower TCO.

### OpenSearch provisioned vs serverless
Benchmark both against normal and peak search shape.

### RDS commitment
Reserved/Database Savings options should follow stable instance-family sizing, not precede it.

### VPC endpoints vs NAT
Compare:
- endpoint hourly cost + endpoint data processing
against
- NAT processed GB + NAT gateway hourly cost.

Use endpoints for security/private connectivity first where required; use economics as the second dimension.

### Telemetry
The fastest place to create accidental cloud spend is uncontrolled logs/traces.

Use:
- sampling,
- retention tiers,
- cardinality limits,
- archive where appropriate,
- business-value review of every high-volume signal.

## Unit economics to track after launch

Track at least:
- cloud cost / completed order,
- cloud cost / 1,000 customer sessions,
- edge transfer cost / session,
- payment integration cost / paid order,
- observability cost / service,
- database cost / order,
- search cost / 1,000 searches.

A growing AWS bill is not necessarily a problem if revenue/orders grow faster. A rising **cost per successful transaction** is the architecture signal.
