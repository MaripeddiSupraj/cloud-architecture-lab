# RADF — AI-Native SDLC Framework

**Source of truth:** `ritru-labs/ritru-ai-native-delivery-framework`  
**Purpose:** Define **how software is delivered with AI agents** safely, governably, and with evidence.

RADF is **not a client** and it is **not an application workload architecture**.

A client architecture answers:

> What should this business system look like in production?

RADF answers:

> How do humans + AI agents take an approved change from intent to production while preserving policy, isolation, verification, approval, and evidence?

## How RADF fits this repository

```
Client / product architecture
        +
RADF AI-native SDLC framework
        ↓
Governed AI-assisted delivery
```

Example:

- Veyra Commerce defines **what Veyra runs**.
- RADF defines **how an engineering team can safely use AI agents to design, implement, verify, release, and operate changes to Veyra or any other system**.
- RithruPay is RADF's reference proof workload, not a separate definition of RADF itself.

## Framework lifecycle

RADF covers the delivery lifecycle from:

```
Business intent
   ↓
Requirements
   ↓
Architecture / threat model
   ↓
Implementation planning
   ↓
Governed AI-agent development
   ↓
Independent verification
   ↓
Security / supply-chain checks
   ↓
Release authorization
   ↓
Deployment
   ↓
Production verification
   ↓
Evidence / audit / metrics
   ↺
```

## Core framework planes

- **Reasoning plane** — AI models and agents propose/perform engineering work.
- **Control plane** — policy, durable workflow, identity, capability, and approvals govern actions.
- **Execution plane** — constrained runtimes perform approved work.
- **Verification plane** — deterministic checks and independent review decide what becomes trusted.
- **Delivery plane** — exact verified artifacts move through governed release paths.
- **Evidence plane** — actions, checks, approvals, releases, incidents, and costs remain traceable.

## Current source maturity

The RADF source repository currently describes:
- **v0.2:** design-ready,
- **v0.3:** reference implementation starting.

So this framework area distinguishes:
- what RADF already defines,
- what still needs implementation proof,
- what technical realization choices remain open.

## Contents

- [Purpose and boundary](01-purpose-and-boundary.md)
- [Source evidence baseline](reviews/01-source-evidence-baseline.md)
- [Framework capability map](architecture/01-capability-map.md)
- [Implementation-decision backlog](adrs/README.md)
- [Framework context diagram](diagrams/00-framework-context.eraserdiagram)
- [Target logical platform](diagrams/01-target-logical-platform.eraserdiagram)

## Rule

This folder must **not** duplicate RADF's full control catalogue, threat catalogue, operating procedures, or governance schemas.

Those remain in the RADF repository.

This folder exists to make the framework understandable as a reusable AI-SDLC delivery architecture and to evaluate how its designed capabilities can be implemented.
