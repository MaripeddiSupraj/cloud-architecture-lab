# ADR-002 — Primary application compute platform

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Platform Engineering

## Context

The primary application services are long-running HTTP and worker processes implemented primarily in Java and Node.js. The team already uses Docker but has only a small platform function. The workload needs horizontal scaling and controlled deployments without creating an infrastructure-management project of its own.

## Options considered

### Amazon ECS on AWS Fargate

**Why it fits**
- ECS provides managed container orchestration.
- Fargate removes EC2 cluster provisioning, patching, and capacity-bin-packing work.
- Long-running Java/Node services fit the container execution model.
- Services can scale independently while retaining familiar container packaging.

**Trade-offs**
- Fargate unit economics can be higher than well-utilized EC2 capacity.
- Less low-level host control.
- AWS-specific orchestration model.

### Amazon EKS

**Why credible**
- Appropriate when Kubernetes APIs/ecosystem, portability, custom scheduling, or existing Kubernetes platform investment are requirements.

**Why not selected now**
- Veyra has no Kubernetes-specific requirement.
- A four-person platform team would inherit additional cluster, add-on, policy, upgrade, and troubleshooting surface.
- Kubernetes portability alone does not justify that operational cost for this engagement.

### ECS on EC2

**Why credible**
- Can improve compute economics at sustained high utilization and provides host-level control.

**Why not selected now**
- Requires instance lifecycle, patching, AMI, capacity, scaling, and bin-packing operations that are not currently justified.
- Normal traffic is far below promotional peak, so permanently managed host capacity risks inefficient baseline utilization.

### AWS Lambda

**Why credible**
- Strong fit for event-driven, bursty, short-duration functions and selected asynchronous tasks.

**Why not selected as the primary platform**
- Core Java/Node commerce services are long-running HTTP workloads with predictable application processes and connection requirements.
- Moving the whole application to functions would require a different application/runtime model solely to use Lambda.

Lambda remains valid for narrow event-driven tasks where it is the simpler execution model.

## Decision

Use **Amazon ECS with AWS Fargate** as the default compute platform for initial long-running application and worker services.

This is a default, not a ban on other compute. A workload may use Lambda, batch, or another service when its execution characteristics justify it.

## Well-Architected impact

- **Operational Excellence:** no worker-node fleet or Kubernetes control-plane ecosystem to operate.
- **Security:** task-level IAM and network boundaries reduce shared-host administration.
- **Reliability:** service desired-count and multi-AZ task placement support instance/AZ failure recovery.
- **Performance Efficiency:** CPU/memory can be set per service and horizontally scaled.
- **Cost Optimization:** pay for task resources rather than an always-on peak-sized cluster; benchmark against ECS/EC2 when sustained utilization grows.
- **Sustainability:** elastic task capacity reduces idle host capacity during normal traffic.

## Validation

- Container load test using representative Java/Node services.
- Measure startup time and scaling response against sale-event ramp assumptions.
- Model monthly Fargate cost against ECS/EC2 at normal and sustained high utilization.

## Revisit triggers

- Sustained compute utilization makes EC2 capacity materially cheaper.
- Kubernetes-native tooling becomes an enterprise requirement.
- Specialized hardware or host control is required.
- Fargate scaling/startup characteristics fail measured peak-event requirements.

## References

- AWS compute decision guide: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-compute-service.html
- AWS container decision guide: https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-container-service.html
