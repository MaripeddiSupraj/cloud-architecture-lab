# Production Readiness Checklist

A diagram is not production readiness. Client 01 is ready only when the following evidence exists.

## Architecture

- [ ] All critical journeys map to accepted ADRs.
- [ ] No unexplained single point of failure.
- [ ] All external dependencies have timeout/retry/idempotency policy.
- [ ] Architecture diagrams match deployed topology.
- [ ] Open assumptions are either resolved or explicitly accepted.

## Performance

- [ ] Per-service RPS/task benchmark complete.
- [ ] ECS scale-out time measured.
- [ ] 15k RPS stress scenario executed.
- [ ] Hot-SKU inventory concurrency test executed.
- [ ] PostgreSQL connection ceiling known.
- [ ] OpenSearch peak query benchmark complete.
- [ ] Cache hit ratio and cold-cache test complete.
- [ ] Provider rate limits tested or contractually documented.

## Reliability

- [ ] ECS task/AZ loss test.
- [ ] RDS Multi-AZ failover test.
- [ ] Cache outage test.
- [ ] OpenSearch degradation test.
- [ ] SQS consumer outage and backlog-drain test.
- [ ] Payment timeout/unknown-state test.
- [ ] Duplicate payment callback test.
- [ ] Transactional outbox recovery test.
- [ ] Backup restore test.
- [ ] Hyderabad DR exercise proves actual RPO/RTO.

## Security

- [ ] Threat model reviewed.
- [ ] Public attack surface enumerated.
- [ ] No public RDS/cache/OpenSearch/task IP path.
- [ ] IAM task roles least-privilege reviewed.
- [ ] CI uses OIDC, not long-lived AWS access keys.
- [ ] Secrets rotation tested.
- [ ] WAF policy validated for false positives.
- [ ] Customer MFA policy approved.
- [ ] Admin/workforce MFA/federation enforced.
- [ ] Log/trace PII review complete.
- [ ] Container/dependency vulnerability policy enforced.
- [ ] Penetration test completed before launch.

## Deployment

- [ ] Build-once/promote-digest verified.
- [ ] Production rollback tested.
- [ ] Expand/contract DB migration tested.
- [ ] Canary/blue-green alarms cause rollback.
- [ ] Emergency change process documented.
- [ ] Terraform drift process documented.

## Observability

- [ ] Service SLIs/SLOs approved.
- [ ] Checkout success dashboard.
- [ ] Payment UNKNOWN backlog alarm.
- [ ] Queue oldest-message alarm.
- [ ] DB connection saturation alarm.
- [ ] Search indexing lag alarm.
- [ ] On-call runbooks linked from alerts.
- [ ] Log and trace retention budgets enforced.

## FinOps

- [ ] AWS Pricing Calculator estimate exported.
- [ ] Expected monthly cost within approved guardrail.
- [ ] CloudFront plan/pay-as-you-go comparison complete.
- [ ] Cognito tier confirmed against product requirements.
- [ ] Fargate ARM benchmark complete.
- [ ] Savings Plan considered only for stable baseline.
- [ ] Cost anomaly alerts enabled.
- [ ] Cost-per-order metric defined.

## Operations

- [ ] Service ownership matrix.
- [ ] 24x7 escalation path for launch window.
- [ ] Payment reconciliation runbook.
- [ ] Database failover runbook.
- [ ] Queue/DLQ redrive runbook.
- [ ] Cache/search degradation runbook.
- [ ] DR declaration/failover/failback runbook.
- [ ] Incident review template and ownership.

## Go-live rule

No single checklist percentage determines launch readiness.

Any unresolved item capable of causing:
- duplicate financial transaction,
- committed-order loss,
- uncontrolled inventory oversell,
- unrecoverable critical data,
- public exposure of sensitive data,
- inability to restore/fail over inside agreed RTO,

is a launch blocker until mitigated or explicitly accepted by the accountable business/security owner.
