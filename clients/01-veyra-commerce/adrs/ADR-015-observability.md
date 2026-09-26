# ADR-015 — Observability and service health architecture

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Platform / SRE / Application Engineering

## Context

Infrastructure being "green" is not enough. Veyra needs to know whether customers can browse, pay, and receive confirmed orders, and engineers need enough telemetry to diagnose distributed failures without reading individual containers manually.

## Options considered

### AWS-native telemetry + OpenTelemetry

Use:
- Amazon CloudWatch metrics, logs, alarms and dashboards,
- ECS Container Insights for task/container infrastructure telemetry,
- OpenTelemetry / AWS Distro for OpenTelemetry for application telemetry,
- distributed traces via AWS-supported tracing/Application Signals capabilities,
- CloudTrail and VPC Flow Logs for control-plane/network investigation.

**Why it fits**
- Strong integration with selected AWS platform.
- Avoids adding a separate commercial observability vendor before telemetry volume/capability needs are understood.
- OpenTelemetry instrumentation reduces application-level lock-in.

### Third-party full-stack SaaS observability

**Why credible**
- Can provide excellent cross-platform correlation, UX, APM and analytics.

**Why deferred**
- Material telemetry ingestion cost and another external dependency.
- Veyra should first define what signals and retention are actually needed.
- OpenTelemetry keeps a future migration/integration path open.

### Logs only

**Why not selected**
- Distributed latency, queue backlog, database contention, and customer failure rates cannot be operated reliably from logs alone.

## Decision

Adopt the AWS-native + OpenTelemetry baseline.

Every production service must emit:

### Golden technical signals
- request rate,
- latency distributions,
- errors,
- saturation/resource utilization.

### Dependency signals
- downstream call latency/error/timeout,
- database pool usage,
- queue depth and oldest-message age,
- cache hit/miss,
- search indexing lag,
- payment reconciliation backlog.

### Business/customer signals
- checkout success rate,
- payment authorization success/unknown state rate,
- order confirmation rate,
- inventory reservation rejection,
- search zero-result rate where meaningful.

## Alert philosophy

Page on symptoms requiring human action, not every resource threshold.

Example:
- high CPU alone -> diagnostic signal,
- checkout success rate below SLO with burn-rate threshold -> page,
- queue backlog growing but safely within processing SLO -> ticket/warning,
- stale UNKNOWN payments beyond reconciliation SLO -> page.

## Telemetry cost controls

- structured logs,
- log-level policy,
- retention tiers,
- sampling for high-volume traces,
- metric-cardinality limits,
- separate audit retention from debug retention.

## Well-Architected impact

- **Operational Excellence:** faster detection, diagnosis and learning.
- **Security:** control-plane/network audit evidence retained centrally.
- **Reliability:** SLOs expose customer impact before resource exhaustion becomes catastrophic.
- **Performance Efficiency:** traces/metrics reveal bottlenecks.
- **Cost Optimization:** telemetry retention/cardinality/sampling are explicitly governed.
- **Sustainability:** avoids retaining/processsing low-value telemetry indefinitely.

## Diagram

[Observability and operations](../diagrams/07-observability-operations.eraserdiagram)

## Reference

ECS application metrics with ADOT: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/application-metrics-cloudwatch.html
