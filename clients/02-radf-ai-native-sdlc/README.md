# Client 02 — RADF AI-Native SDLC Platform

**Source system:** `ritru-labs/ritru-ai-native-delivery-framework`  
**Domain:** AI-native software delivery / governed agentic engineering  
**Architecture mode:** Evidence-based review of an existing real framework  
**Current source status:** v0.2 design-ready; v0.3 reference implementation starting

## Why this case exists

Client 01 proves a conventional high-scale digital commerce architecture.

Client 02 asks a different architecture question:

> How should a production-grade AI-native software delivery platform execute autonomous/agentic engineering work safely, durably, economically, and with verifiable evidence?

RADF is not treated as a fictional requirement set. Architecture decisions in this case must trace back to the actual RADF repository.

## Source-of-truth rule

The RADF repository remains the source of truth for:
- framework principles,
- controls,
- threats,
- governance schemas,
- SDLC procedures,
- RithruPay reference scenario.

This architecture-lab case owns:
- cloud/platform realization,
- workload decomposition,
- service-selection ADRs,
- capacity/cost reasoning,
- deployment topology,
- production-readiness review.

Do not duplicate RADF framework documentation here.

## Current evidence

- [Case context](engagement/01-case-context.md)
- [Source evidence baseline](reviews/01-source-evidence-baseline.md)
- [Capability architecture](architecture/01-capability-map.md)
- [Architecture decision backlog](adrs/README.md)
- [RADF system context](diagrams/00-system-context.eraserdiagram)
- [Target logical platform](diagrams/01-target-logical-platform.eraserdiagram)

## Key architecture rule

RADF already defines a critical separation:

**reasoning != control != execution**

AI proposes/executes within policy. Deterministic authorization and verification decide what becomes trusted. Production authority is separate from development authority.

The cloud architecture must preserve that separation physically and operationally.
