# RADF Implementation Decision Backlog

These ADRs do **not** define a client architecture.

They decide how the reusable RADF framework capabilities can be implemented as a working reference platform.

## Decision backlog

| ADR | Implementation question | Status |
|---|---|---|
| ADR-001 | Control-plane runtime: ECS, EKS, or serverless? | Proposed |
| ADR-002 | Agent isolation: container, Kubernetes Job, sandbox, or microVM? | Proposed |
| ADR-003 | Durable workflow engine: Temporal, Step Functions, or another engine? | Proposed |
| ADR-004 | Workflow/evidence metadata store | Proposed |
| ADR-005 | Evidence artifact store | Proposed |
| ADR-006 | Policy decision implementation | Proposed |
| ADR-007 | Task identity and short-lived credential broker | Proposed |
| ADR-008 | Tool/MCP registry and gateway | Proposed |
| ADR-009 | Context/RAG/memory implementation | Proposed |
| ADR-010 | Evidence event pipeline | Proposed |
| ADR-011 | Git provider authentication model | Proposed |
| ADR-012 | CI/CD and artifact-bound production authorization | Proposed |
| ADR-013 | Project/client isolation model | Proposed |
| ADR-014 | Agent egress enforcement | Proposed |
| ADR-015 | Agent/workflow observability | Proposed |
| ADR-016 | Model gateway/provider routing | Proposed |
| ADR-017 | Token/tool/cloud cost controls | Proposed |
| ADR-018 | Framework control-plane backup/recovery | Proposed |

## Acceptance standard

Every implementation ADR must answer:

- Which RADF capability/control requires this?
- Which threats does it mitigate?
- What alternatives exist?
- Why is this option appropriate for a reusable AI-SDLC framework?
- What operational/security/cost burden does it add?
- What proof is required before calling it implementation-proven?

RADF principles remain vendor-neutral even when the reference implementation chooses a concrete technology.
