# RADF Platform Capability Map

## Architecture separation

RADF's core architecture separates:

1. **Reasoning plane** — models/agents propose plans and engineering actions.
2. **Control plane** — policy, identity, approvals, durable orchestration and evidence govern what may happen.
3. **Execution plane** — constrained runtimes and tools perform approved actions.
4. **Verification plane** — deterministic and independent checks decide what becomes trusted.
5. **Delivery plane** — CI/CD promotes exact verified artifacts under authorization.
6. **Evidence plane** — immutable/durable records connect intent to action, check, approval and release.

## Capability groups

### Intake and intent
- client/project boundary,
- project policy,
- intent/spec/plan,
- risk classification.

### Durable orchestration
- workflow state,
- retries/timeouts,
- fencing/leases,
- stale-result rejection,
- escalation.

### Agent/runtime
- task-scoped worker,
- isolated workspace,
- CPU/memory/runtime limits,
- controlled egress,
- disposable execution.

### Authorization
- agent identity,
- capability grants,
- short-lived credentials,
- policy decision point,
- approval service.

### Tool access
- tool/MCP registry,
- action classification,
- scoped tool gateway,
- Git/CI/cloud/security integrations.

### Context and knowledge
- project-scoped context,
- approved knowledge retrieval,
- memory policy,
- model/provider policy.

### Verification
- compile/type/lint,
- tests,
- security scanning,
- independent review,
- environment/deployment verification.

### Evidence
- evidence events,
- normalized metadata,
- large artifacts,
- SBOM/provenance,
- audit records,
- traceability graph.

### Delivery
- PR,
- CI,
- immutable artifact,
- approval binding,
- staging/production gate,
- rollback.

### Operations
- runtime telemetry,
- workflow health,
- model/tool/token cost,
- incidents/RCA,
- recovery.

## Different scaling dimensions

RADF does not scale like a normal customer API.

Important capacity dimensions include:

- concurrent engineering tasks,
- concurrent model calls,
- sandbox startup rate,
- workflow duration,
- repository checkout/build size,
- CI concurrency,
- tool/API rate limits,
- context retrieval volume,
- evidence volume,
- token/model spend,
- long-running idle workflows awaiting human approval.

This is why the compute/orchestration/storage architecture must not be copied from Client 01.
