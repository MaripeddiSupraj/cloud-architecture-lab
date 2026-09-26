# Terraform Implementation Blueprint

## Objective

Translate the accepted architecture into implementation without creating one giant state file or one giant reusable module.

## Repository layout

~~~
infra/
├── bootstrap/
│   ├── state-backend/
│   └── github-oidc/
│
├── modules/
│   ├── network/
│   ├── edge/
│   ├── ecs-service/
│   ├── rds-postgresql/
│   ├── elasticache/
│   ├── opensearch/
│   ├── messaging/
│   ├── object-storage/
│   ├── identity/
│   ├── observability/
│   └── security-baseline/
│
├── live/
│   ├── nonprod/
│   │   └── ap-south-1/
│   ├── production/
│   │   └── ap-south-1/
│   └── dr/
│       └── ap-south-2/
│
└── policies/
    ├── tfsec-checkov/
    └── policy-as-code/
~~~

## State boundaries

Do not use one state for the whole platform.

Recommended state separation:
- organization/account baseline,
- production network,
- edge,
- shared security/observability,
- data services,
- application platform,
- individual high-risk data domains when lifecycle requires isolation,
- DR infrastructure.

A failed application deployment must not lock or risk the organization's account baseline state.

## Bootstrap

Bootstrap separately:
1. Terraform state backend.
2. State encryption/versioning/locking controls.
3. GitHub OIDC trust.
4. CI execution roles.
5. Break-glass administration path.

Normal Terraform CI must use short-lived federated AWS credentials.

## Module rule

A module exists when multiple compositions need a stable abstraction.

Do not create wrappers around every AWS resource merely to call them modules.

Good reusable module examples:
- ECS service pattern,
- production VPC pattern,
- RDS PostgreSQL baseline,
- CloudFront/WAF edge pattern.

A wrapper that mirrors one AWS resource argument-for-argument usually adds little value.

## Environment composition

Environment directories own:
- CIDRs,
- Region,
- scaling minimum/maximum,
- instance/cache/search sizing,
- DNS names,
- retention values,
- feature flags,
- DR settings.

Modules own reusable implementation patterns.

## Deployment order

### Foundation
1. Accounts/governance.
2. Networking.
3. Identity/security.
4. Observability baseline.

### Data
5. S3.
6. RDS.
7. Valkey.
8. OpenSearch.
9. EventBridge/SQS.

### Runtime
10. ECR.
11. ECS clusters/services.
12. ALB.
13. CloudFront/WAF/Cognito integration.

### DR
14. Hyderabad network/recovery prerequisites.
15. Database replica.
16. Object/image/secret replication.
17. Recovery automation.

## Terraform CI

Pull request:
- fmt,
- validate,
- provider/module lock verification,
- security scan,
- speculative plan,
- policy checks,
- human review for production-impacting changes.

Merge:
- approved apply using OIDC,
- save plan/apply evidence,
- emit audit event,
- run post-change smoke validation.

## Database schema

Terraform provisions database infrastructure.

Application schema migrations belong to the application release process, not Terraform.

## Secrets

Terraform may provision the secret container/policy, but plaintext secret values must not be committed to Terraform configuration or normal plan output.

Use generated/rotated secrets and workload retrieval where possible.

## DR rule

The DR environment must be deployable from the same modules with regional composition changes.

If disaster recovery depends on undocumented console configuration, it is not a reproducible DR design.

## First implementation slices

### Slice 1 — platform skeleton
- production-like nonprod VPC,
- ALB,
- ECS/Fargate sample service,
- ECR,
- GitHub OIDC,
- CloudWatch,
- Secrets Manager/KMS.

### Slice 2 — transactional path
- PostgreSQL Multi-AZ,
- Checkout/Inventory sample,
- small connection pools,
- failure-safe migrations.

### Slice 3 — read/async path
- Valkey,
- OpenSearch,
- EventBridge/SQS,
- outbox relay.

### Slice 4 — edge/security
- CloudFront,
- WAF,
- Cognito Lite,
- private origin controls.

### Slice 5 — DR
- Hyderabad replica/recovery infrastructure,
- failover exercise.

Each slice must produce test evidence before the next one becomes production-approved.
