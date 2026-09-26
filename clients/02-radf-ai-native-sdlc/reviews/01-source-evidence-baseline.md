# RADF Source Evidence Baseline

**Reviewed source:** `ritru-labs/ritru-ai-native-delivery-framework` on `main`  
**Review date:** 2026-09-26

## Source status

The RADF README states:
- **v0.2 design-ready**
- **v0.3 reference implementation starting**

The v0.2 readiness checklist explicitly freezes further framework-design work unless implementation, red-team testing, a real client, an external-standard change, or engineering ambiguity exposes a gap.

## Design assets already present

The source repository contains evidence for:

- gated AI-native SDLC lifecycle,
- 118 formal controls,
- 60 AI/agent/software-delivery threats,
- governance schemas,
- client and SDLC operating procedures,
- responsibility model,
- control verification matrix,
- twelve architecture views,
- RithruPay prototype scope,
- REQ-1842 validation scenario,
- implementation phases P0–P10,
- production/release/security principles.

## Required v0.3 capabilities still identified as implementation work

The current RADF roadmap lists these as outstanding:

| Capability | Source status |
|---|---|
| Policy engine | Not yet implemented |
| Identity broker / short-lived credentials | Not yet implemented |
| Durable orchestrator | Not yet implemented |
| Sandbox runtime | Not yet implemented |
| Tool/MCP registry and gateway | Not yet implemented |
| Context/knowledge gateway | Not yet implemented |
| Evidence/event pipeline | Not yet implemented |
| Approval service | Not yet implemented |
| CI/CD integrations | Not yet implemented |
| Evaluation harness | Not yet implemented |
| Reference cloud architecture | Not yet implemented |

Therefore the architecture-lab must not label these as existing production components.

## Existing target technical direction

The RithruPay technical-stack document proposes:

- Python + FastAPI control-plane services,
- real durable workflow engine; Temporal named as a strong candidate,
- OPA-compatible policy decisions,
- ephemeral Kubernetes Jobs/Pods for agent execution,
- PostgreSQL for evidence metadata,
- object/artifact storage for large evidence,
- OpenTelemetry,
- project-scoped knowledge/vector/object storage,
- cloud-native secrets/workload identity.

These are **design candidates**, not automatically accepted architecture decisions.

## Required proof stages from the source roadmap

P0–P10 require progressively proving:
- manual baseline delivery,
- evidenced task state,
- durable workflow recovery,
- isolated agent execution,
- authorization/capability enforcement,
- Git/CI/security/cloud integration,
- production-like release,
- REQ-1842 end-to-end,
- controlled incident,
- red-team cases,
- sanitized demonstration evidence.

## Architecture-lab consequence

This case will use three labels consistently:

- **SOURCE-PROVEN** — directly evidenced in the RADF repository.
- **TARGET-DESIGN** — specified by RADF but not yet implementation-proven.
- **LAB-DECISION** — concrete cloud/platform decision made in this architecture case.

No target design will be presented as an implemented capability until repository/runtime evidence supports it.
