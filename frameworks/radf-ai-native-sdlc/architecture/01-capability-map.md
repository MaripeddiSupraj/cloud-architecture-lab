# RADF Framework Capability Map

RADF describes the capabilities needed to achieve a governed AI-native SDLC.

## 1. Intent and governance

- business intent,
- requirements/specification,
- project policy,
- risk classification,
- autonomy level,
- approval requirements.

## 2. Reasoning

- planner/engineering agents,
- approved model providers,
- task decomposition,
- context-aware reasoning.

## 3. Durable control

- workflow state,
- retries/timeouts,
- leases/fencing,
- escalation,
- stale-result rejection,
- human approval binding.

## 4. Agent identity and authorization

- Agent Cards,
- task capability grants,
- short-lived credentials,
- policy decision point,
- environment-specific permission.

## 5. Constrained execution

- isolated task workspace,
- CPU/memory/runtime limits,
- network egress policy,
- approved tool set,
- disposable runtime,
- no inherited developer credentials.

## 6. Tool and MCP governance

- registered tools,
- action classification,
- read/write separation,
- policy-checked invocation,
- Git/CI/cloud/security integrations,
- auditable tool calls.

## 7. Context, knowledge and memory

- project-scoped context,
- approved retrieval,
- provider/model boundary,
- memory policy,
- client isolation.

## 8. Independent verification

- compile/type/lint,
- unit/integration/contract tests,
- security scanning,
- performance/resilience checks where required,
- independent review,
- deployment verification.

## 9. Governed release

- immutable artifacts,
- SBOM/provenance,
- artifact-bound approval,
- environment-specific release policy,
- rollback readiness,
- production health verification.

## 10. Evidence and audit

- workflow transitions,
- policy allow/deny,
- agent/tool actions,
- test/security results,
- approval records,
- deployment evidence,
- incident/RCA evidence.

## 11. Operations and improvement

- workflow/agent telemetry,
- model/token/tool cost,
- incidents,
- recovery,
- baseline vs AI-assisted delivery metrics,
- continuous control improvement.

## Architectural separation

The framework deliberately keeps:

```
Reasoning
   ↓
Control
   ↓
Execution
   ↓
Verification
   ↓
Release
   ↓
Evidence
```

No single AI agent should simultaneously reason, authorize itself, execute unrestricted actions, declare its own output trusted, and deploy to production.
