# Planning-Grade Monthly Cost Estimate

**Estimate date:** 2026-09-26  
**Primary:** Asia Pacific (Mumbai)  
**DR:** Asia Pacific (Hyderabad)  
**FX reference:** 1 USD = INR 95.8175  
**Confidence:** planning grade, not procurement grade

## Purpose

This estimate answers one architecture question:

> Is the selected design direction plausibly compatible with the INR 20–25 lakh/month normal-operation guardrail before we invest in implementation?

It is intentionally a **range** because several services depend on measured traffic and benchmark results.

## Current verified pricing anchors

### ECS/Fargate — Mumbai Linux/x86

Planning rate:
- vCPU: ~$0.04256/vCPU-hour
- memory: ~$0.004655/GB-hour

Current baseline requested capacity:
- 21.5 vCPU
- 43 GiB
- 730 hours/month

Estimated baseline:

~~~
CPU    ≈ $668/month
Memory ≈ $146/month
Total  ≈ $814/month
~~~

A planning example of four large sale windows adding an average 80 vCPU + 160 GiB for 16 total hours/month adds only about **$66** of Fargate compute. Real task sizes and scale duration must replace this assumption.

### Cognito Lite — 600k MAU

From ADR-020:

**~$2,795/month**

For sensitivity:
- Essentials: ~ $8,850/month
- Plus: ~ $12,000/month

Identity tier alone can move the monthly bill by several lakh INR.

### CloudFront — India data transfer anchor

Current AWS material continues to show India on-demand transfer at approximately:
- first 10 TB: $0.109/GB
- next 40 TB: $0.085/GB

Transfer-only scenarios:

| Delivery | Approx transfer cost |
|---|---:|
| 10 TB | ~$1.1k |
| 25 TB | ~$2.4k |
| 50 TB | ~$4.6k |

HTTP/HTTPS request charges are additional under pay-as-you-go.

AWS also offers newer flat-rate CloudFront plans that bundle CDN, WAF, DDoS protection, DNS, logging and other features. Those plans must be compared with pay-as-you-go after monthly request count is known.

### PostgreSQL planning candidate

For cost modelling only, use a **db.r7g.2xlarge-class memory envelope (8 vCPU / 64 GiB)** as the first benchmark candidate.

Current public price-list-derived references show roughly:
- Mumbai PostgreSQL db.r7g.2xlarge: ~$1.088/hour Single-AZ.

Planning Multi-AZ compute:

~~~
1.088 × 730 × 2 ≈ $1,588/month
~~~

For 1.5 TiB gp3 planning capacity, using a current Mumbai reference around $0.131/GB-month and accounting for Multi-AZ replicated storage gives roughly another **$400/month** before backup/extra IOPS/throughput.

A Hyderabad DR read replica of the same class plus storage adds roughly **$1,000/month** before cross-Region transfer.

Therefore this candidate produces a planning database/DR line near **$3,000/month**.

If benchmark evidence requires a 16-vCPU/128-GiB class, that database line can move toward **$5k–$6k/month**.

## Planning cost envelope

These are architecture ranges, not exact invoice predictions.

| Cost group | Low | Expected | High | Main uncertainty |
|---|---:|---:|---:|---|
| CloudFront + WAF + requests | $1,500 | $3,000 | $5,500 | request count / pricing plan / delivery TB |
| Cognito Lite | $2,795 | $2,795 | $2,795 | tier fixed; SMS/email excluded |
| ECS/Fargate | $800 | $1,000 | $1,500 | measured task resources |
| RDS primary + DR | $3,000 | $4,000 | $6,000 | benchmark class / storage / transfer |
| OpenSearch | $500 | $900 | $1,500 | serverless vs provisioned; QPS |
| Valkey | $250 | $600 | $1,000 | working set and ECPU rate |
| S3 + replication | $250 | $400 | $700 | growth/versioning/replication |
| ALB + NAT + endpoints | $250 | $500 | $900 | LCUs / provider egress / endpoint mix |
| CloudWatch + traces | $200 | $500 | $1,000 | log/trace volume |
| SQS/EventBridge/ECR/KMS/Secrets/Route53 | $150 | $300 | $600 | request/event volume |
| **Total** | **~$9.7k** | **~$14.0k** | **~$21.5k** | — |

At the current FX reference:

| Scenario | Approx INR/month |
|---|---:|
| Low | ~₹9.3 lakh |
| Expected | ~₹13.4 lakh |
| High | ~₹20.6 lakh |

## Guardrail conclusion

The selected architecture is **plausibly inside the INR 20–25 lakh/month normal-operation guardrail**, even with meaningful HA/DR spend.

However, three decisions dominate the confidence interval:

1. **Customer identity tier**
2. **CloudFront traffic/request model**
3. **PostgreSQL production instance size**

If Cognito Essentials replaced Lite, add roughly **$6,055/month (~₹5.8 lakh)** at 600k MAU.

If Cognito Plus replaced Lite, add roughly **$9,205/month (~₹8.8 lakh)**.

Those upgrades could move a high-cost month beyond the guardrail, so identity feature requirements must be treated as architecture/FinOps decisions.

## Important exclusions

Not included:
- payment gateway fees,
- courier/ERP vendor fees,
- SMS/WhatsApp/email provider charges,
- taxes,
- AWS Enterprise Support,
- engineering labour,
- marketplace security/search products,
- data migration project costs.

## What converts this to a procurement-grade estimate

1. Run the ECS benchmark harness.
2. Confirm PostgreSQL instance class with load test.
3. Measure monthly CloudFront request count and transfer.
4. Benchmark OpenSearch provisioned vs serverless.
5. Measure Valkey working-set GB and ECPU demand.
6. Measure telemetry GB/day.
7. Confirm external-provider NAT egress.
8. Recreate the architecture in AWS Pricing Calculator and export the estimate.

Until then, use this document for **architecture feasibility**, not budget approval.
