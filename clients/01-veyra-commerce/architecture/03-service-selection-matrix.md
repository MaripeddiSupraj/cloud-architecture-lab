# Service Selection Matrix — Why This / Why Not That

This is the quick learning view of the accepted architecture. The ADRs remain the authoritative decision records.

| Capability | Selected | Why it fits Veyra | Why not the obvious alternative now |
|---|---|---|---|
| Application decomposition | Coarse-grained domain services | Independent scaling/failure boundaries without dozens of deployables | Fine-grained microservices add network, CI/CD, data and on-call complexity before the team needs it |
| Long-running compute | ECS on Fargate | Containers, independent scaling, no node fleet, small platform team | EKS adds Kubernetes operations without a Kubernetes-specific requirement; ECS/EC2 adds host lifecycle; Lambda is not the default shape for long-running Java/Node APIs |
| Public edge | CloudFront + WAF | Edge cache, origin protection, web security | Direct ALB gives up edge/cache protection; API Gateway is not needed for every internal web/mobile API |
| Application ingress | ALB | Natural routing/load-balancing for ECS HTTP services | API Gateway becomes valuable when API product/client governance is a real requirement |
| Transaction database | RDS PostgreSQL Multi-AZ | ACID, constraints, joins, team skill, managed HA | DynamoDB shifts relational correctness into application modelling; Aurora capability is not yet required by measured load |
| Product search | OpenSearch Service | Full-text, facets, ranking, independent query scaling | PostgreSQL search would compete with OLTP and offers less search-specific capability |
| Cache | ElastiCache Serverless for Valkey | Bursty demand, shared low-latency cache, no node sizing at launch | Node-based Valkey may become cheaper at stable high usage; local cache alone cannot share state across tasks |
| Async work | SQS | Durable buffering, retries, backpressure, per-consumer isolation | Direct synchronous calls couple checkout to downstream health |
| Domain event routing | EventBridge | Fan-out/routing without producer knowing every consumer | Kafka/MSK adds a streaming operating model before replay/ordered-log requirements exist |
| Media/static storage | S3 | Object semantics, durability, lifecycle, CloudFront integration | EFS/filesystem semantics are unnecessary; database BLOBs burden OLTP/backup |
| Customer identity | Cognito User Pools | Managed auth rather than custom password/token infrastructure | Custom auth creates large security/operations scope |
| Workforce access | IAM Identity Center | Federation and centrally governed AWS access | IAM users/static credentials weaken lifecycle and audit |
| Workload identity | ECS task IAM roles | Short-lived per-workload AWS permissions | Shared access keys create leakage and rotation risk |
| Secrets | Secrets Manager | Central encrypted/auditable secrets and rotation/replication | GitHub variables/config files duplicate secrets into delivery systems |
| Network | 3-AZ layered VPC | Public ingress, private apps, isolated data | One subnet/tier weakens reachability and failure boundaries |
| Internet egress | NAT Gateway per app AZ | AZ-local resilient outbound path to payment/ERP/etc. | Single NAT creates AZ dependency/cross-AZ path; Network Firewall is not justified without inspection mandate |
| IaC | Terraform | Existing team skill, plan/review workflow, strong AWS ecosystem | CloudFormation/CDK/Pulumi are credible but switching tools does not solve a current requirement |
| CI/CD | GitHub Actions + ECR + ECS deployment strategies | Source-aligned workflow, build once/promote digest, OIDC identity | Jenkins creates a CI platform to operate; Argo CD is Kubernetes-centric |
| Critical releases | ECS canary / blue-green | Controlled production validation and quick rollback | Rolling exposes more production traffic before validation |
| Low-risk releases | ECS rolling + circuit breaker | Lower duplicate-capacity cost with automatic failure stop/rollback | Blue/green for every worker spends extra capacity without equal risk reduction |
| Observability | CloudWatch + OpenTelemetry/ADOT | AWS integration plus portable instrumentation | Third-party SaaS remains an option after telemetry requirements/cost are measured |
| DR | Mumbai primary + Hyderabad pilot-light/warm data | Meets India launch/residency posture while avoiding full active-active cost/complexity | Backup-only risks missing RTO; active-active makes order/inventory/payment consistency much harder |
