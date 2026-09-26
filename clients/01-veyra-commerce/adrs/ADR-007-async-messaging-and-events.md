# ADR-007 — Asynchronous messaging and domain events

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Platform / Commerce Engineering

## Context

Order notifications, search indexing, fulfilment integration, analytics, reconciliation, and similar work should not extend the synchronous customer transaction when completion is not required before responding.

The system needs both:
- durable work queues with backpressure, and
- event routing/fan-out between independently owned consumers.

There is currently no requirement for a long-retention streaming log, very high partition throughput, or arbitrary historical consumer replay.

## Options considered

### Amazon SQS + Amazon EventBridge

**Why it fits**
- SQS provides durable queues for decoupled worker processing, retry isolation, and backpressure.
- EventBridge provides event routing/fan-out without making producers know every consumer.
- SQS queues can sit behind event consumers so a slow/downstream service does not force synchronous failure upstream.
- Both minimize broker/cluster operations.

**Trade-offs**
- At-least-once delivery requires idempotent consumers.
- Event schemas and ownership still need governance.
- EventBridge is not a substitute for a replayable streaming log when that becomes a real requirement.

### Amazon MSK / Kafka

**Why credible**
- Strong fit for high-throughput event streams, ordered partitions, multiple independent consumers, long-retention event logs, and replay-centric processing.

**Why not selected initially**
- Current event volume and use cases do not require Kafka semantics.
- The team does not currently have deep Kafka operational expertise.
- Introducing brokers/topics/partition planning and a streaming operating model before a requirement exists would increase complexity.

### Amazon SNS only

**Why credible**
- Simple publish/subscribe fan-out.

**Why not sufficient as the standard**
- Veyra also needs durable consumer work queues, retry isolation, and buffering. SNS may still be used in a narrower pattern where its semantics are the simplest fit.

### Direct service-to-service calls for all integration

**Why not selected**
- Notification, indexing, analytics, and fulfilment side effects should not make checkout availability depend on downstream service availability.

## Decision

Use:
- **Amazon SQS** for durable asynchronous work and per-consumer buffering.
- **Amazon EventBridge** for domain-event routing and fan-out.

Use the **transactional outbox pattern** for events that must be emitted atomically with relational state changes. Consumers must be idempotent.

## Well-Architected impact

- **Operational Excellence:** no messaging cluster to patch or rebalance.
- **Security:** queue/bus permissions can be scoped per producer/consumer.
- **Reliability:** downstream failures are buffered and isolated from customer transactions.
- **Performance Efficiency:** async workers scale independently from request-serving services.
- **Cost Optimization:** request-based managed messaging avoids an always-on broker fleet at current scale.
- **Sustainability:** workers can consume based on backlog instead of remaining peak-provisioned.

## Validation

- Prove duplicate delivery is safe.
- Test consumer outage and backlog drain.
- Verify DLQ/redrive procedures.
- Test outbox recovery after application/database interruption.
- Measure event age and queue depth under peak traffic.

## Revisit triggers

- Need for long-retention ordered streams or arbitrary replay.
- Sustained event throughput makes streaming economics/semantics preferable.
- Multiple analytics consumers need the same ordered history.
- Change-data-capture requirements become central.

## References

- Amazon SQS: https://docs.aws.amazon.com/sqs/
- AWS SQS/SNS/EventBridge decision guide: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/sns-or-sqs-or-eventbridge.html
