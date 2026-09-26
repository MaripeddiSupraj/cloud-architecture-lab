# Initial Threat Model

This is an architecture-level threat model, not a substitute for application-specific secure design or penetration testing.

## Assets

High-value assets include:
- customer PII,
- credentials/tokens,
- orders and payment references,
- inventory truth,
- promotion/pricing rules,
- administrative functions,
- CI/CD identities and artifacts,
- encryption keys/secrets,
- audit evidence.

## Trust boundaries

1. Public internet -> CloudFront/WAF.
2. Edge -> ALB.
3. ALB -> private ECS workloads.
4. Workloads -> data services.
5. Workloads -> AWS control/data-plane APIs.
6. Workloads -> third-party providers via egress.
7. CI/CD -> AWS accounts.
8. Workforce -> AWS/admin interfaces.
9. Primary Region -> DR Region.

## Priority threats and controls

| Threat | Primary controls |
|---|---|
| Credential stuffing/account takeover | Managed customer identity, MFA/risk controls where required, rate/bot controls, anomaly signals |
| Web exploit/injection | WAF as edge layer plus secure coding, validation, dependency/SAST/DAST testing |
| DDoS/traffic abuse | CloudFront edge, AWS baseline protection, WAF/rate controls, service max/backpressure |
| Direct data-store exposure | Private/isolated subnets, SG least-path access, no public DB/cache/search |
| Stolen AWS static credential | OIDC/Identity Center/task roles; prohibit normal long-lived AWS access keys |
| Secret leakage | Secrets Manager, secret scanning, rotation, no secrets in images/repos |
| CI artifact tampering | immutable digest promotion, restricted ECR write, scan/supply-chain evidence |
| Privilege escalation | least-privilege roles, SCP/Control Tower controls, CloudTrail, separation of production/security accounts |
| Duplicate/replayed payment callback | signed/provider-authenticated callback where available, unique provider reference, idempotent state transition |
| Checkout replay/duplicate charge | idempotency keys and existing-result lookup |
| Inventory race | authoritative atomic DB reservation |
| SSRF/egress abuse | no public task IP, egress controls, metadata/credential hardening, optional firewall/proxy if threat requires |
| Log/telemetry PII leakage | structured logging standard, field allow/deny policy, restricted retention/access |
| Malicious deletion/corruption | backups, versioning, cross-account/Region recovery where appropriate, tested restore |
| Compromised admin account | federation, MFA, least privilege, privileged session auditing, break-glass controls |

## Security decisions still requiring business input

- customer MFA policy,
- bot/fraud ownership and acceptable false-positive rate,
- formal compliance requirements beyond payment scope,
- data retention/deletion periods,
- customer PII field classification,
- third-party provider assurance requirements,
- mandatory egress inspection,
- penetration testing cadence.

## Security test gates

Production readiness requires:
- external attack-surface review,
- IAM least-privilege review,
- secret scan clean,
- container/dependency critical vulnerability policy,
- WAF test,
- direct data-tier reachability test,
- privilege/escalation review,
- payment callback spoof/replay test,
- backup/restore and audit-log integrity test.
