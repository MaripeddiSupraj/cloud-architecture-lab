# Data Transfer and Telemetry Model

## Purpose

Network and observability spend can exceed compute spend in customer-facing systems. These volumes must be modelled explicitly.

## Product media

Current media footprint: ~8 TB stored.

Initial monthly delivery scenarios:

| Scenario | Customer media delivery | CloudFront hit ratio | Approximate origin read |
|---|---:|---:|---:|
| Low | 10 TB | 90% | 1 TB |
| Expected | 25 TB | 90% | 2.5 TB |
| High campaign | 50 TB | 90% | 5 TB |

The edge-delivered volume is the key internet transfer cost driver. Origin reads matter for S3 request/load and cache efficiency.

## Dynamic API transfer

Planning assumption:
- average compressed API response: 8–20 KB,
- average request payload: 1–4 KB,
- monthly API request count must be derived from actual traffic curve, not peak RPS × entire month.

Do not multiply 15k RPS by 30 days; that would treat a short event peak as permanent demand.

## NAT egress

NAT should carry only traffic that truly requires public internet egress:
- payment provider,
- courier/provider APIs,
- legacy enterprise endpoints where private connectivity is unavailable.

Avoid routing S3 and selected AWS API traffic through NAT when private endpoints are justified.

Track:
- GB to each provider,
- NAT gateway processed GB,
- cross-AZ bytes,
- retry amplification during provider incidents.

## Cross-AZ transfer

Three-AZ resilience can create avoidable transfer cost if workloads constantly cross AZ boundaries.

Design goals:
- NAT egress stays AZ-local,
- application-to-data cross-AZ behavior is measured,
- load balancer cross-zone behavior is understood,
- do not trade reliability away merely to eliminate transfer charges.

## Cross-Region DR traffic

Track separately:
- PostgreSQL cross-Region replication,
- S3 replication/recovery copies,
- ECR image replication,
- Secrets replication,
- DR tests.

Normal production cost and DR replication cost must be visible as separate line items.

## Logging baseline

Initial structured log budget:

| Category | Budget |
|---|---:|
| Application logs | 5–8 GB/day |
| ALB/WAF/edge logs retained centrally | 2–5 GB/day after filtering/aggregation assumptions |
| Security/audit logs | workload-dependent; separate retention |
| Total planning envelope | **10 GB/day** initially |

10 GB/day ~= 300 GB/month before archival/compression differences.

High-cardinality debug logging during peak events must not become the default production level.

## Tracing

Default:
- 100% trace propagation,
- sampled trace export,
- higher sampling for errors/critical checkout journeys,
- lower sampling for healthy high-volume browse traffic.

Initial exported trace budget: 50–100 GB/month until actual span volume is measured.

## Metrics

Prefer bounded-cardinality dimensions.

Never place unconstrained values such as:
- customer ID,
- order ID,
- product ID,
- request ID

into metric dimensions.

Use logs/traces for those identifiers.

## FinOps alarms

Create cost anomaly/budget controls for:
- CloudFront transfer,
- NAT processed data,
- CloudWatch log ingestion,
- trace ingestion,
- OpenSearch,
- cross-Region transfer/replication.

A traffic incident should not silently become a cost incident.
