# ADR-011 — Identity, workload credentials, secrets, and encryption

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Security Architecture / Platform

## Context

Veyra needs customer authentication, workforce access, service-to-AWS authorization, application secrets, encryption controls, and auditable privileged access. These are separate identity problems and must not be solved with one shared credential mechanism.

## Decision

Use four distinct control planes:

1. **Amazon Cognito User Pools** for customer identity at launch, subject to final migration requirements.
2. **AWS IAM Identity Center** federated to the enterprise IdP when available for workforce AWS access.
3. **ECS task IAM roles** for workload-to-AWS permissions.
4. **AWS Secrets Manager** for secrets that cannot be replaced with workload identity, protected by KMS and rotation where practical.

## Why this approach

### Customer identity — Cognito

**Why selected**
- Removes password/auth token implementation from commerce services.
- Supports standards-based application authentication flows.
- Keeps customer identity separate from workforce AWS administration.

**Why not custom authentication**
- Credential storage, MFA, token issuance, account recovery, attack protection, and auth security are not differentiating commerce capabilities.

**Why not force workforce identity into Cognito**
- Workforce authorization and AWS administrative access have different lifecycle, assurance, and federation requirements.

### Workload identity — ECS task roles

**Why selected**
- Tasks receive scoped temporary AWS credentials.
- Avoids long-lived AWS access keys in environment files or CI/CD variables.

**Why not shared IAM user keys**
- Long-lived static keys create avoidable rotation and leakage risk and weaken workload-level least privilege.

### Secrets — Secrets Manager

**Why selected**
- Central encrypted storage, IAM access control, auditability, rotation integration, and regional replication capability.
- Appropriate for database/provider credentials that cannot be removed through identity federation.

**Why not repository or CI variables as the system of record**
- Secrets would be duplicated across delivery systems and harder to rotate/audit.

### Encryption — KMS

Use service-integrated encryption at rest. Customer-managed keys are used where access-policy separation, audit, cross-account requirements, or contractual controls justify them; AWS-managed keys remain acceptable where a customer-managed key adds no control value.

## Network rule

Secrets are consumed from private workloads. Interface VPC endpoints are used where the security/cost model justifies private AWS API access.

## Well-Architected impact

- **Operational Excellence:** centralized identity/secrets lifecycle.
- **Security:** least privilege and short-lived workload credentials.
- **Reliability:** secret/identity responsibilities do not depend on application-local files.
- **Performance Efficiency:** managed identity services avoid running authentication infrastructure.
- **Cost Optimization:** customer-managed KMS keys/endpoints are introduced only where their control value justifies recurring cost.
- **Sustainability:** removes duplicated self-hosted identity/secrets infrastructure.

## Validation

- Attempt cross-service access with the wrong task role.
- Rotate a provider/database secret without rebuilding an image.
- Validate customer token expiry/revocation flows.
- Confirm CloudTrail records privileged secret/IAM operations.
- DR test replicated regional secrets used by the recovery environment.

## Revisit triggers

- Existing enterprise CIAM platform is mandated.
- Customer migration/identity federation requirements exceed Cognito fit.
- Contractual HSM/key-custody requirements change.

## References

- ECS task IAM roles: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html
- Secrets Manager replication: https://docs.aws.amazon.com/secretsmanager/latest/userguide/replicate-secrets.html
