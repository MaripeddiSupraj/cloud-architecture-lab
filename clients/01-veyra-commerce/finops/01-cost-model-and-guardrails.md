# FinOps Cost Model and Guardrails

## Objective

The normal-operation architecture target is approximately INR 20–25 lakh/month before exceptional campaign spikes and selected third-party SaaS charges.

The estimate must be traceable to workload assumptions. "AWS bill should be around X" is not an architecture estimate.

## Cost model

Monthly cost is modelled by driver rather than service name alone.

| Cost domain | Primary drivers to capture |
|---|---|
| CloudFront | requests, internet data transfer, cache hit ratio |
| WAF | web ACL/rules and inspected requests |
| ALB | running hours and LCU dimensions |
| ECS/Fargate | task vCPU, memory, architecture, running seconds |
| RDS PostgreSQL | instance class, Multi-AZ, storage, IOPS/throughput, backups, data transfer |
| OpenSearch | provisioned/serverless compute, storage, ingest/query pattern |
| ElastiCache | serverless data/requests or node-hours if later changed |
| SQS/EventBridge | messages/events, payload/request units, archives if used |
| S3 | stored GB, requests, retrieval/lifecycle, replication |
| NAT | gateway-hours and processed GB |
| VPC endpoints | endpoint-hours and processed GB for interface endpoints |
| Observability | log ingestion/storage/query, metrics, traces, dashboards |
| Security | Secrets, KMS requests/keys, Config/security services, optional firewall |
| DR | cross-Region database replica, replicated storage/images/secrets, data transfer, standby network/app capacity |

## Required pricing record

Every formal estimate must record:

- estimate date,
- pricing source,
- primary and DR Region,
- currency/exchange-rate assumption,
- tax excluded/included state,
- normal traffic,
- promotional traffic,
- data transfer assumptions,
- log/trace retention,
- reserved/savings commitment assumptions,
- excluded third-party services.

## Baseline vs peak

Do not combine them.

### Baseline
Normal month:
- 700–1,200 RPS envelope,
- ~35k orders/day,
- normal media/cache/search traffic,
- production minimum task count,
- normal telemetry.

### Peak/event
Model separately:
- up to 15k RPS,
- ~150k orders/day,
- pre-scaled ECS tasks,
- increased WAF/CloudFront/ALB requests,
- cache/search load,
- queue traffic,
- database capacity headroom,
- provider traffic.

The question is not "Can we afford 15k RPS all month?" when the business only expects it for sale windows.

## Cost guardrails

- Every resource has owner, environment, service and cost-center tags where supported.
- AWS Budgets / cost anomaly alerting are configured at account/workload level.
- Non-production has shutdown/scale-down policy where technically safe.
- Logs/traces have explicit retention; indefinite debug retention is prohibited.
- S3 version/lifecycle policies prevent unbounded obsolete media.
- Fargate sizing is reviewed from measured CPU/memory percentiles.
- OpenSearch/Valkey modes are re-evaluated after real steady-state usage.
- NAT and interface endpoint spend is reviewed together, not independently.
- Cross-AZ and cross-Region data transfer is included in architecture reviews.
- Savings Plans/commitments are considered only after a stable baseline is observed.

## Cost decisions already made by architecture

The following expensive components were deliberately **not** selected without a proven requirement:

- EKS/Kubernetes platform layer,
- Kafka/MSK,
- full multi-Region active-active application,
- AWS Network Firewall,
- database-per-service,
- blue/green deployment for every background workload,
- permanent peak-sized compute.

## Next estimate gate

A formal monthly price estimate will be produced after the capacity model resolves:
1. per-domain RPS,
2. average/peak response and request payload,
3. ECS CPU/memory from a representative benchmark,
4. database storage/IOPS and instance benchmark,
5. CloudFront internet transfer and hit ratio,
6. OpenSearch query/index benchmark,
7. cache data size/request rate,
8. telemetry GB/day,
9. NAT/provider egress GB,
10. DR replication volumes.

Pricing without these values would create false precision.
