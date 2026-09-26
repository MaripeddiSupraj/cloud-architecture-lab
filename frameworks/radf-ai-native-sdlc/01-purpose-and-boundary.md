# RADF Purpose and Boundary

## What RADF is

RADF is a reusable **AI-native software delivery framework**.

It defines the control model for using AI agents throughout the software-development lifecycle while keeping human accountability, deterministic policy, isolation, independent verification, and production authorization.

It can be applied to:
- web/SaaS systems,
- APIs,
- mobile applications,
- data platforms,
- AI applications,
- regulated workloads.

## What RADF is not

RADF is not:
- a client,
- an e-commerce architecture,
- a banking architecture,
- a single cloud deployment template,
- a coding-agent product with unrestricted authority,
- a replacement for the target application's own architecture.

A target system still needs its own business requirements, NFRs, capacity model, cloud architecture, security design, DR, and FinOps.

RADF governs **how changes to that system are delivered with AI**.

## Non-negotiable framework properties

1. Humans remain accountable.
2. The model is not the policy engine.
3. Agent capability is explicit and scoped.
4. Execution uses constrained environments.
5. Client/project context and credentials are isolated.
6. AI output is untrusted until verified.
7. Important actions generate durable evidence.
8. Development permission never implies production permission.
9. Durable workflow state survives worker failure/restart.
10. Unauthorized actions fail closed.

## Reference proof

RADF uses RithruPay and REQ-1842 as a reference validation workload.

The purpose of RithruPay is to prove that RADF can govern a realistic change across:
- requirements,
- architecture,
- code,
- database,
- security,
- testing,
- CI/CD,
- approval,
- deployment,
- observability,
- incident/recovery.

RithruPay is therefore **a framework validation scenario**, not "Client 02" of this cloud architecture portfolio.

## Relationship to client architectures

A client in this repository may optionally have a section such as:

```
delivery-framework/
└── radf-adoption.md
```

That document would explain which RADF risk tier, autonomy level, approvals, agent capabilities, and evidence controls apply to that specific client.

The client architecture itself remains independent.
