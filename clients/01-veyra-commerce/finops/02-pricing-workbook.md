# AWS Pricing Workbook

**Pricing date:** 2026-09-26  
**Primary Region:** Asia Pacific (Mumbai) — ap-south-1  
**DR Region:** Asia Pacific (Hyderabad) — ap-south-2  
**FX reference:** 1 USD = INR 95.8175 on 2026-09-26.

## Purpose

This workbook separates:
1. architecture quantities we can estimate now,
2. official AWS pricing dimensions,
3. unit prices that must be captured from the AWS Pricing Calculator for the exact Region/configuration,
4. measured usage that must replace planning assumptions later.

The goal is to avoid false precision.

## AWS pricing facts used

### Fargate

AWS prices Fargate from requested:
- vCPU,
- memory,
- operating system,
- CPU architecture,
- additional ephemeral storage.

Billing is per second with a one-minute minimum for Linux tasks. AWS also states that Compute Savings Plans can reduce stable Fargate usage, while Spot can be used for interruption-tolerant ECS tasks.

### RDS PostgreSQL

RDS PostgreSQL On-Demand pricing depends on:
- DB instance class,
- Region,
- deployment option,
- storage,
- I/O/throughput where applicable,
- backups/data transfer.

For Multi-AZ with one standby, RDS maintains a standby in another Availability Zone and can fail over automatically.

### Other variable services

Exact cost depends on workload dimensions for:
- CloudFront data transfer/requests,
- WAF requests/rules,
- ALB hours/LCUs,
- OpenSearch capacity/storage,
- ElastiCache Serverless data/requests,
- SQS requests,
- EventBridge events,
- S3 storage/requests/replication,
- NAT Gateway hours/processed GB,
- CloudWatch logs/metrics/traces,
- Cognito MAUs/features.

## Architecture quantities to enter into AWS Pricing Calculator

### ECS/Fargate — normal month

Use the current 26-task baseline:

| Service | Tasks | Task size | Hours/month |
|---|---:|---|---:|
| Experience API | 6 | 1 vCPU / 2 GiB | 730 |
| Catalog/Pricing | 6 | 1 vCPU / 2 GiB | 730 |
| Cart | 3 | 0.5 vCPU / 1 GiB | 730 |
| Checkout/Orders | 3 | 1 vCPU / 2 GiB | 730 |
| Inventory | 3 | 1 vCPU / 2 GiB | 730 |
| Search worker | 2 | 1 vCPU / 2 GiB | 730 |
| Integration worker | 3 | 0.5 vCPU / 1 GiB | 730 |

Baseline requested capacity:
- **21.5 vCPU continuously**
- **43 GiB memory continuously**

This is before autoscaling above minimum.

### Promotional burst

Do **not** price peak task count for 730 hours.

Model sale windows separately. Example monthly campaign assumption:

- four large events/month,
- each event has 4 hours of pre-scale/peak/recovery,
- additional capacity above baseline averages 80 vCPU / 160 GiB during that 16-hour monthly window.

Replace with actual campaign calendar.

### RDS PostgreSQL

Calculator entries:
- PostgreSQL,
- Multi-AZ one standby,
- Graviton instance family benchmark candidate,
- 1.2 TB current data plus index/growth headroom,
- choose gp3/general purpose unless benchmark proves provisioned IOPS required,
- automated backup retention,
- cross-Region read replica in Hyderabad as separate DR line.

Do **not** purchase Reserved/Database Savings commitment before baseline sizing stabilizes.

### CloudFront

Create three scenarios:
- 10 TB/month,
- 25 TB/month expected,
- 50 TB/month high campaign.

Use:
- India-heavy viewer distribution,
- actual HTTP request count,
- expected 90% product-media cache hit target.

### S3

Model:
- 8 TB current product/media data,
- growth rate,
- request count,
- versioning overhead,
- lifecycle,
- cross-Region recovery copy/replication.

### OpenSearch

Pricing Calculator must evaluate both:
1. provisioned OpenSearch Service,
2. OpenSearch Serverless,

using:
- 450k products,
- derived index size from benchmark,
- ~3,000 peak search RPS stress,
- normal search QPS,
- index ingest/change rate,
- HA requirement.

The cheaper mode cannot be chosen from peak RPS alone.

### ElastiCache

Compare:
1. ElastiCache Serverless for Valkey,
2. node-based Valkey,

using:
- working-set GB,
- requests/sec,
- bytes/request,
- normal-versus-peak ratio.

Serverless stays the launch default until real usage proves node-based economics better.

### NAT Gateway

Base fixed architecture:
- 3 NAT Gateways in production, one per application AZ.

Variable:
- processed GB only for external/public provider traffic.

Do not count S3 traffic if it uses the S3 Gateway Endpoint.

### Observability

Initial monthly assumptions:
- logs: ~300 GB/month,
- traces: 50–100 GB/month exported,
- bounded-cardinality application metrics,
- high-resolution ECS metrics only for services where faster scaling has measured benefit.

### Cognito

Use 600k MAU as the identity planning baseline and select the actual Cognito feature tier only after MFA/risk/advanced-security requirements are confirmed.

Identity pricing can be material at this user count and must not be hidden inside "miscellaneous."

## Required output

The final calculator-backed estimate must contain:

| Cost group | Baseline USD | Baseline INR | Peak/event increment | Confidence |
|---|---:|---:|---:|---|
| Edge + WAF | TBD | TBD | TBD | Usage model pending |
| ALB | TBD | TBD | TBD | Medium |
| ECS/Fargate | TBD | TBD | TBD | Benchmark pending |
| RDS primary | TBD | TBD | — | DB size benchmark pending |
| DR database | TBD | TBD | — | Medium |
| OpenSearch | TBD | TBD | TBD | Benchmark pending |
| Valkey | TBD | TBD | TBD | Usage pending |
| S3 | TBD | TBD | TBD | Medium |
| SQS/EventBridge | TBD | TBD | TBD | Medium |
| NAT/endpoints | TBD | TBD | TBD | Provider transfer pending |
| CloudWatch/OTel | TBD | TBD | TBD | Telemetry pending |
| Cognito | TBD | TBD | TBD | Feature tier pending |
| **Total** | **TBD** | **TBD** | **TBD** | — |

## Why there is no invented monthly total yet

The architecture has a INR 20–25 lakh/month planning guardrail, but a defensible estimate needs:
- regional AWS unit prices for the exact SKU/configuration,
- real task benchmark sizes,
- media transfer,
- Cognito tier,
- OpenSearch/Valkey usage,
- telemetry volume.

Publishing a precise number before those are resolved would make the repository look confident rather than correct.

## Pricing sources

- AWS Fargate Pricing: https://aws.amazon.com/fargate/pricing/
- Amazon RDS for PostgreSQL Pricing: https://aws.amazon.com/rds/postgresql/pricing/
- Amazon OpenSearch Service Pricing: https://aws.amazon.com/opensearch-service/pricing/
- Amazon ElastiCache Pricing: https://aws.amazon.com/elasticache/pricing/
- Elastic Load Balancing Pricing: https://aws.amazon.com/elasticloadbalancing/pricing/
- Amazon VPC Pricing: https://aws.amazon.com/vpc/pricing/
- Amazon CloudFront Pricing: https://aws.amazon.com/cloudfront/pricing/
- Amazon S3 Pricing: https://aws.amazon.com/s3/pricing/
- Amazon CloudWatch Pricing: https://aws.amazon.com/cloudwatch/pricing/
- Amazon Cognito Pricing: https://aws.amazon.com/cognito/pricing/
