# ADR-014 — CI/CD and production release strategy

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Platform Engineering / Application Engineering

## Context

The platform needs frequent releases without turning each deployment into a customer-risk event. Veyra source is maintained in GitHub and workloads run on ECS/Fargate.

## Decision

Use a **build-once, promote-by-image-digest** pipeline:

1. Pull request validation: lint, unit, contract, dependency, secret and SAST checks.
2. Build container once after merge.
3. Push immutable image to Amazon ECR.
4. Scan image and generate/retain software supply-chain metadata.
5. Deploy the exact image digest to DEV.
6. Run integration/security checks.
7. Promote the same digest to STAGE.
8. Run smoke, migration and representative load checks.
9. Policy/approval gate to production.
10. Observe deployment using health checks, SLO and business alarms.
11. Automatically or operationally roll back to the previous known-good digest on failure.

GitHub Actions authenticates to AWS using short-lived federation/OIDC; no long-lived AWS access key is stored as a GitHub secret.

## ECS deployment choice

### Critical customer transaction services

Use **Amazon ECS native canary or blue/green deployment** with CloudWatch alarm-based rollback where practical.

Why:
- checkout/order/inventory releases justify lower production blast radius,
- new revisions can receive controlled traffic before full cutover,
- rollback is faster than replacing an already fully rolled-out bad version.

### Lower-risk/background services

Use **rolling deployment with ECS deployment circuit breaker and automatic rollback** where duplicate full environments add little value.

## Why not use one deployment strategy everywhere?

Deployment safety should match business impact. Running blue/green capacity for every low-value worker wastes cost without equivalent risk reduction.

## Why GitHub Actions instead of Jenkins?

- No current requirement justifies operating Jenkins controllers, plugins, upgrades, credentials, and executor capacity.
- The source workflow already lives in GitHub.
- Hosted/self-hosted runner choice can remain workload-specific.

## Why not Argo CD?

Argo CD is Kubernetes-centric. Veyra selected ECS, so adding Kubernetes-style GitOps tooling solely for delivery would be unnecessary complexity.

## Why not force AWS CodePipeline?

CodePipeline is credible and integrates deeply with AWS, but the existing engineering workflow is GitHub-centric. A second CI orchestration plane is not justified unless governance requires it.

## Database release rule

Schema changes use **expand/contract** compatibility:
- deploy additive/backward-compatible schema first,
- deploy application versions that can operate across the transition,
- migrate/backfill,
- remove old fields only after old application versions are gone.

A production rollback must never depend on reversing an unsafe destructive migration.

## Well-Architected impact

- **Operational Excellence:** repeatable releases and automated rollback.
- **Security:** short-lived CI credentials and security gates.
- **Reliability:** canary/blue-green reduces blast radius for critical paths.
- **Performance Efficiency:** pre-production load validation catches resource regressions.
- **Cost Optimization:** expensive parallel environments only for risk-sensitive workloads.
- **Sustainability:** build once and reuse artifacts rather than rebuilding per environment.

## Diagram

[CI/CD and release](../diagrams/06-cicd-release.eraserdiagram)

## References

- ECS deployment strategies: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_service-options.html
- ECS canary deployments: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/canary-deployment.html
