# ADR-012 — Production network topology and egress

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Network Architecture / Platform / Security

## Context

The platform must accept internet customer traffic while preventing direct public access to application tasks and data stores. Application workloads also require outbound access to payment, ERP, courier, and messaging providers.

## Decision

Use one production VPC spanning three Availability Zones with three network layers:

### Public ingress layer
- Internet-facing Application Load Balancer across public subnets.
- NAT Gateway per AZ for private workload internet egress that is actually required.
- No application task or database receives a public IP.

### Private application layer
- ECS/Fargate tasks in private subnets using awsvpc networking.
- Task security groups accept application traffic only from approved upstream security groups.
- Service/data access is explicitly allowed rather than CIDR-wide.

### Isolated data layer
- RDS, Valkey, and VPC-access OpenSearch have no internet route.
- Data security groups only allow required application/service sources.

## AWS service access

- Use an S3 Gateway VPC Endpoint for private S3 access.
- Evaluate interface endpoints for ECR, Secrets Manager, CloudWatch Logs, and other high-value/private service access.
- Do **not** automatically create every possible interface endpoint: each endpoint has recurring/data processing cost and must be justified by security or traffic economics.

## Egress

External provider calls leave through the NAT Gateway in the task's AZ.

A NAT Gateway is deployed per application AZ to avoid making all egress dependent on one AZ and to reduce unnecessary cross-AZ paths.

## Why not AWS Network Firewall at launch?

AWS Network Firewall is valuable when the requirement includes centralized L3-L7 inspection, explicit domain/IP egress filtering, IDS/IPS-style network controls, or compliance-mandated traffic inspection.

It is **not selected initially** because:
- public HTTP attacks are handled at CloudFront/WAF,
- workload/data reachability is constrained with security groups and routing,
- no current requirement mandates deep centralized egress inspection,
- it adds hourly/processing cost and an additional network failure/operations layer.

Add it if threat modelling, compliance, or egress-governance requirements justify it.

## Why not place ECS tasks in public subnets?

A public IP is unnecessary for workloads already reached through ALB and would create a larger attack/reachability surface.

## Why not one subnet tier?

It would mix resources with different reachability and data sensitivity, weakening failure/security boundaries.

## Well-Architected impact

- **Operational Excellence:** standard ingress/app/data zones make ownership and troubleshooting predictable.
- **Security:** data stores are not directly reachable from the internet; SG references enforce least-path access.
- **Reliability:** multi-AZ application/egress removes a single-AZ dependency.
- **Performance Efficiency:** local-AZ egress and endpoints reduce avoidable routing.
- **Cost Optimization:** interface endpoints and firewall services are selected selectively, not ceremonially.
- **Sustainability:** avoid unnecessary inspection/endpoint infrastructure with no measured need.

## Validation

- Prove direct internet -> ECS/RDS/Valkey/OpenSearch connections fail.
- Remove one AZ/NAT and verify application service remains available in remaining AZs.
- Verify security groups do not use unrestricted database ingress.
- Validate provider egress and endpoint DNS behavior.
- Review VPC Flow Logs during failure/security tests.

## Diagram

[Network & security diagram](../diagrams/02-network-security.eraserdiagram)

## References

- AWS Well-Architected network layers: https://docs.aws.amazon.com/wellarchitected/latest/framework/sec_network_protection_create_layers.html
- ECS network security: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-network.html
