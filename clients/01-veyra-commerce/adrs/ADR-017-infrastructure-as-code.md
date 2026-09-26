# ADR-017 — Infrastructure as Code and configuration strategy

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Platform Engineering

## Context

All environments must be reproducible, reviewable, and recoverable. Infrastructure changes need the same traceability and peer review as application code.

## Options considered

### Terraform

**Why it fits**
- Team already has Terraform skills.
- Mature AWS provider/ecosystem.
- Declarative plan/review workflow fits pull-request governance.
- Keeps infrastructure modules distinct from application implementation language.

**Trade-offs**
- State must be protected and operationally governed.
- Provider/module upgrades require testing.
- Poor module design can create coupling just as easily as hand-built infrastructure.

### AWS CloudFormation / CDK

**Why credible**
- Native AWS provisioning and deep service support.
- CDK can improve developer experience for teams wanting infrastructure expressed through programming languages.

**Why not selected as default**
- Current platform team already has Terraform capability; changing IaC language does not solve a current requirement.

### Pulumi

**Why credible**
- Strong typed programming-language model and multi-cloud providers.

**Why not selected for this engagement**
- Again, current team skill and delivery speed favor Terraform. Pulumi remains a valid alternative if type-safe infrastructure-as-software becomes a concrete team requirement.

### Console-driven infrastructure

**Why not selected**
- Weak reproducibility, review, drift control and DR recovery.

## Decision

Use **Terraform** as the default infrastructure provisioning tool.

Principles:
- separate reusable modules from environment composition,
- keep state/environment blast radius bounded,
- no shared "god state" for the whole organization,
- remote protected state with versioning/encryption/locking controls appropriate to the chosen backend,
- pull-request plan before apply,
- production apply through CI with short-lived identity,
- policy/security checks before production,
- provider/module versions pinned and upgraded intentionally,
- importing existing resources is preferred over silently recreating them.

## Configuration

- infrastructure topology -> Terraform,
- runtime non-secret application configuration -> versioned configuration/deployment definitions,
- secrets -> Secrets Manager,
- container image version -> immutable ECR digest,
- emergency console changes -> documented break-glass followed by IaC reconciliation.

## Well-Architected impact

- **Operational Excellence:** repeatable change/recovery.
- **Security:** reviewable IAM/network changes and no secrets in Terraform source.
- **Reliability:** DR environment can be recreated/tested from code.
- **Performance Efficiency:** environment sizing/scaling settings are versioned.
- **Cost Optimization:** tags/budgets/resource choices are enforceable in code.
- **Sustainability:** unused infrastructure can be identified and removed systematically.

## Revisit triggers

- Enterprise mandates another IaC platform.
- Terraform operating/state constraints materially impede delivery.
- Platform abstraction evolves into a higher-level internal developer platform requiring another provisioning interface.
