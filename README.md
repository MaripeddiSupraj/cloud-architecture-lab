# Cloud Architecture Lab

A portfolio of end-to-end cloud architecture engagements plus reusable architecture and delivery frameworks.

The repository separates **what a client system should run** from **how engineering work can be delivered**.

## Working principles

- Requirements before services.
- Capacity before topology.
- Trade-offs before preferences.
- Managed services where they reduce undifferentiated operations.
- Security and reliability are design inputs, not later additions.
- Cost is an architectural constraint.
- Every major technology choice requires an ADR.
- Complexity must be justified by measurable need.
- Architecture is incomplete until deployment, observability, recovery, and operations are covered.

## Client architecture engagements

| Client | Domain | Status |
|---|---|---|
| [01 — Veyra Commerce](clients/01-veyra-commerce/README.md) | Digital commerce | Production design / implementation readiness |

Client folders answer:

> **What production architecture does this workload need, and why?**

They contain requirements, NFRs, capacity, architecture decisions, diagrams, security, reliability, FinOps, DR, and implementation readiness.

## Reusable frameworks

| Framework | Purpose | Status |
|---|---|---|
| [RADF — AI-Native SDLC](frameworks/radf-ai-native-sdlc/README.md) | How humans + AI agents safely deliver software from intent to production | Design-ready framework / reference implementation evolving |

Framework folders answer a different question:

> **How should a reusable engineering capability or operating model work across many clients and workloads?**

RADF is therefore not "Client 02." A client or product may **adopt RADF** as its AI-native delivery framework while retaining its own independent cloud/application architecture.

## Repository model

```
clients/
  01-veyra-commerce/
  future-client-architectures/

frameworks/
  radf-ai-native-sdlc/
  future-reusable-patterns/
```

The Veyra organization/scenario is fictional and used for architecture learning. RADF is based on the real Rithru Labs AI-native delivery framework and is represented here only at the level needed to explain its reusable architecture and implementation choices.
