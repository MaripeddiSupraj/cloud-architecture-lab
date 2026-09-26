# Initial Well-Architected Review

**Review point:** Architecture definition  
**Date:** 2026-09-26  
**Purpose:** Identify design strengths and unresolved production risks before implementation.

This is not a score. A green-looking scorecard can hide serious workload-specific risks; the review records evidence and actions.

## Operational Excellence

### Current strengths
- Infrastructure and service decisions are ADR-driven.
- Terraform is the selected IaC baseline.
- CI/CD uses build-once/promote-digest and risk-based deployment strategies.
- Runbooks and failure tests are explicit production-readiness requirements.

### Open actions
- Define service ownership/on-call rota.
- Write payment reconciliation, DB failover, cache-loss and DR runbooks.
- Define change failure rate, deployment frequency, MTTR and incident-review process.

## Security

### Current strengths
- CloudFront/WAF public edge.
- ECS private tasks and isolated data tier.
- Workload IAM roles instead of static AWS keys.
- Cognito/workforce identity separation.
- Secrets Manager and KMS strategy.
- Multi-account security/log separation.

### Open actions
- Complete threat-model review with business/security owners.
- Define WAF managed/custom rule policy and bot/fraud boundary.
- Define key ownership/rotation policy.
- Confirm data classification/retention/deletion requirements.
- Decide whether centralized egress inspection is contractually required.

## Reliability

### Current strengths
- Multi-AZ application placement and RDS Multi-AZ.
- Cache/search treated as non-authoritative.
- SQS buffering and transactional outbox.
- Idempotent checkout/payment recovery model.
- Cross-Region PostgreSQL replica and DR prerequisites.

### High-priority risks
- Actual provider payment SLA/rate/timeout behavior is still unknown.
- Inventory hot-SKU contention is unproven.
- DR RPO/RTO is a target until exercised.
- External ERP/warehouse consistency ownership remains open.

## Performance Efficiency

### Current strengths
- Browse/search/cache scale independently from OLTP.
- ECS target tracking plus planned event pre-scaling.
- CloudFront removes media/static load from application origin.
- Search uses a dedicated derived index.

### Open actions
- Build request distribution by domain rather than aggregate RPS.
- Establish per-service CPU/memory/request scaling correlation.
- Measure database connection/transaction ceiling.
- Benchmark OpenSearch and cache cold-start behaviour.
- Measure task startup time against sale traffic ramp.

## Cost Optimization

### Current strengths
- Fargate avoids idle peak EC2 cluster at launch.
- Serverless cache chosen for variable traffic pending economics.
- Kafka/EKS/Network Firewall/active-active were not added without requirements.
- Risk-based deployment avoids blue/green capacity for every workload.

### Open actions
- Produce dated Mumbai/Hyderabad price snapshot.
- Measure CloudFront egress/cache hit ratio.
- Compare Fargate vs ECS/EC2 after sustained utilization is known.
- Compare ElastiCache Serverless vs node-based after real cache GB/requests.
- Review NAT data processing versus interface-endpoint economics.
- Set log/trace retention and cardinality budgets.

## Sustainability

### Current strengths
- Elastic compute rather than permanent sale-event capacity.
- CDN/cache reduce repeated origin work.
- Minimal DR application standby rather than full active-active.
- Lifecycle/retention policies are architecture requirements.

### Open actions
- Measure idle minimum task capacity.
- Define S3 object lifecycle and obsolete version cleanup.
- Remove unused telemetry and duplicate data copies.
- Review service right-sizing quarterly.

## Architecture risks requiring evidence before Gate C

1. Payment unknown-state recovery passes failure injection.
2. Hot-SKU inventory test cannot oversell.
3. Database connections remain safe during ECS scale-out/failover.
4. 15k RPS event test meets latency SLO without uncontrolled downstream overload.
5. DR exercise proves <=5 minute RPO and <=30 minute RTO or requirements/design are changed.
6. Security review proves data services have no unintended public path.
7. Monthly cost estimate stays within agreed normal-operation guardrail or business accepts the variance.
