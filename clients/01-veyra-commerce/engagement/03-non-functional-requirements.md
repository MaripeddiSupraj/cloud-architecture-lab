# Non-Functional Requirements

## Availability

Initial service objectives:

| Capability | Availability target |
|---|---:|
| Checkout and payment-facing platform functions | 99.99% |
| Product browse and cart | 99.95% |
| Search | 99.90% |
| Administrative functions | 99.50% |
| Offline analytics/reporting | 99.00% |

Availability targets apply to Veyra-controlled components and must be measured independently from third-party provider availability.

## Performance

Initial customer-facing objectives:

- General API latency: P50 < 150 ms, P95 < 400 ms, P99 < 1 s where the response does not synchronously depend on an external provider.
- Search: P95 < 500 ms under normal operating conditions.
- Static and media delivery should use edge delivery where it materially improves latency and origin protection.
- Performance tests must include normal load, expected peak, and sudden traffic-step scenarios.

## Resilience

The design must tolerate the loss of a single availability zone without loss of committed transactional data.

Failures in notification, analytics, recommendations, or other non-critical downstream functions must not block successful order placement.

External calls require bounded timeouts and controlled retries. Retry behaviour must avoid amplification during provider degradation.

## Recovery

Initial business targets:

- Critical transactional data: RPO <= 5 minutes pending workload-level refinement.
- Critical customer transactions: RTO <= 30 minutes pending workload-level refinement.
- Less critical data sets may use wider objectives if justified by business impact and restoration cost.

Recovery requirements will be assigned per data domain rather than copied universally.

## Security

The platform must provide:

- Encryption in transit and at rest.
- Central identity and least-privilege authorization for workforce access.
- Workload identities instead of long-lived infrastructure credentials where supported.
- Managed secret storage and rotation processes.
- Auditable administrative activity.
- Network segmentation between public entry points, application workloads, and data services.
- Protection against common web threats, abusive automation, and volumetric attacks.
- Dependency, source, image, and infrastructure security checks in delivery pipelines.
- Defined vulnerability remediation ownership and severity-based SLAs.

Raw payment-card data must not be stored by the commerce platform. Payment scope should be minimized through a compliant external payment provider and tokenized references.

## Operability

Production services must expose sufficient metrics, logs, traces, health signals, and business indicators to diagnose customer-impacting failures.

Alerts must represent actionable symptoms or risk, not simply low-level resource thresholds.

## Deployability

Production releases must be repeatable, auditable, and reversible. Deployment strategy may vary by workload based on statefulness, risk, and rollback characteristics.

## Cost efficiency

The platform must be sized for normal-day economics while retaining controlled mechanisms to absorb promotional peaks. Permanent peak provisioning requires explicit justification.
