# RADF Architecture Decision Records

RADF source design is treated as input, not as unquestioned implementation truth.

## Decision backlog

| ADR | Question | Status |
|---|---|---|
| ADR-000 | Concrete cloud implementation baseline | Proposed |
| ADR-001 | Control-plane compute: ECS vs EKS vs serverless | Proposed |
| ADR-002 | Agent execution runtime: containers vs Kubernetes Jobs vs stronger sandbox/microVM | Proposed |
| ADR-003 | Durable orchestration: Temporal vs Step Functions vs application-owned workflow | Proposed |
| ADR-004 | Authoritative workflow/evidence metadata store | Proposed |
| ADR-005 | Evidence artifact/object store | Proposed |
| ADR-006 | Policy decision architecture: OPA vs embedded rules vs cloud-native authorization | Proposed |
| ADR-007 | Agent/task identity and short-lived credential broker | Proposed |
| ADR-008 | Tool/MCP gateway architecture | Proposed |
| ADR-009 | Context/RAG/memory storage and isolation | Proposed |
| ADR-010 | Event backbone and evidence pipeline | Proposed |
| ADR-011 | GitHub authentication: GitHub App vs PAT/token patterns | Proposed |
| ADR-012 | CI/CD integration and production authorization | Proposed |
| ADR-013 | Client/project tenancy and isolation | Proposed |
| ADR-014 | Network egress and destination enforcement | Proposed |
| ADR-015 | Observability and agent/workflow telemetry | Proposed |
| ADR-016 | AI/model gateway and provider routing | Proposed |
| ADR-017 | Model/token/tool/cloud cost controls | Proposed |
| ADR-018 | Backup, recovery and workflow continuity | Proposed |
| ADR-019 | Control-plane deployment/release strategy | Proposed |

## Acceptance standard

Every decision must show:
- RADF requirement/control it satisfies,
- threat(s) it mitigates,
- workload characteristics,
- credible alternatives,
- why selected,
- why alternatives are rejected/deferred,
- operational burden,
- security consequences,
- cost consequences,
- evidence required to prove the decision.
