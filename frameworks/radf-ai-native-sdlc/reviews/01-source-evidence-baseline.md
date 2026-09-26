# RADF Source Evidence Baseline

**Reviewed source:** `ritru-labs/ritru-ai-native-delivery-framework` on `main`  
**Review date:** 2026-09-26

## Current framework maturity

The RADF source states:
- **v0.2 design-ready**
- **v0.3 reference implementation starting**

The framework-design baseline is intentionally frozen unless implementation, red-team testing, a real engagement, a standards change, or an engineering ambiguity exposes a gap.

## Already defined in RADF

The source repository contains:

- gated AI-native SDLC lifecycle,
- 118 formal controls,
- 60 AI/agent/software-delivery threats,
- governance schemas,
- client/SDLC operating procedures,
- responsibility model,
- control-verification matrix,
- 12 architecture views,
- RithruPay reference scope,
- REQ-1842 scenario,
- P0–P10 implementation phases,
- release/security/production principles.

## Still requiring implementation proof

The current roadmap identifies these v0.3 capabilities as implementation work:

| Capability | Status |
|---|---|
| Policy engine | Target design |
| Identity broker / short-lived credentials | Target design |
| Durable orchestrator | Target design |
| Sandbox runtime | Target design |
| Tool/MCP registry and gateway | Target design |
| Context/knowledge gateway | Target design |
| Evidence/event pipeline | Target design |
| Approval service | Target design |
| CI/CD integrations | Target design |
| Evaluation harness | Target design |
| Reference cloud architecture | Target design |

## Candidate technical direction

The RADF/RithruPay design currently proposes candidates such as:

- Python/FastAPI control-plane services,
- a real durable workflow engine,
- OPA-compatible policy decisions,
- ephemeral isolated agent workers,
- PostgreSQL for normalized metadata,
- object storage for large evidence,
- OpenTelemetry,
- project-scoped context/knowledge storage,
- cloud-native secrets and workload identity.

These remain implementation choices to validate; they are not framework principles.

## Labels used here

- **SOURCE-DEFINED** — specified by RADF.
- **IMPLEMENTATION-PROVEN** — backed by working implementation evidence.
- **IMPLEMENTATION-CANDIDATE** — a technical realization still to be decided/proved.

This prevents the framework design from being confused with deployed production capability.
