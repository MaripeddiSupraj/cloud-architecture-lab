# ADR-013 — AWS account and environment isolation

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Cloud Governance / Security / Platform

## Context

Veyra needs production isolation, centralized audit/security visibility, and lower blast radius for developer/admin mistakes. A single AWS account is operationally simple but makes permissions, quotas, billing, logs, and production boundaries harder to govern safely.

## Options considered

### One AWS account for all environments

**Why credible**
- Lowest setup overhead.

**Why not selected**
- Weak production/non-production isolation.
- Larger IAM and quota blast radius.
- Harder centralized security/log retention boundaries.

### Account per microservice/team

**Why credible**
- Very strong isolation and ownership.

**Why not selected**
- Excessive account/network/IAM/observability overhead for the current service/team count.

### AWS Organizations + Control Tower landing zone

**Why it fits**
- Provides a governed multi-account foundation rather than hand-building every account baseline.
- Separates production workloads from security/logging responsibilities.
- Supports centralized controls while preserving workload-team autonomy.

## Decision

Adopt AWS Organizations with an AWS Control Tower landing zone.

Initial workload structure:

- **Management account** — organization/billing/governance only; no production workload.
- **Log Archive account** — centralized immutable-ish organization audit/config logs.
- **Audit/Security account** — security tooling and cross-account investigation.
- **NonProduction account** — dev/test integration workloads initially.
- **Production account** — customer production workloads.
- **Shared Services account** — only when a genuinely shared platform capability appears; do not create shared services merely to fill an account diagram.

As the engineering organization grows, dev/stage may split into separate accounts based on blast radius, quota, or ownership needs.

## Why not put production in the management account?

The organization management account is highly privileged. Production workload execution there would unnecessarily combine billing/governance privilege with application runtime.

## Well-Architected impact

- **Operational Excellence:** consistent guardrails/account vending.
- **Security:** stronger production and audit/log separation.
- **Reliability:** quotas and accidental changes are less likely to cross environment boundaries.
- **Performance Efficiency:** workloads retain independent quotas/configuration.
- **Cost Optimization:** consolidated billing plus account-level cost attribution.
- **Sustainability:** avoids duplicating every platform component into unnecessary per-service accounts.

## Validation

- Test SCP/control effects against non-production accounts first.
- Confirm production engineers do not require management-account access.
- Verify CloudTrail/Config organization logging reaches the Log Archive account.
- Test break-glass procedure and auditability.

## References

- AWS Control Tower accounts: https://docs.aws.amazon.com/controltower/latest/userguide/what-shared.html
- Control Tower with Organizations: https://docs.aws.amazon.com/controltower/latest/userguide/organizations.html
