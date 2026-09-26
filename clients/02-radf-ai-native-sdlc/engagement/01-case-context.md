# Case Context

## Existing platform intent

RADF is Rithru Labs' AI-native delivery framework for moving client work from discovery through production using humans and AI agents while preserving policy, accountability, isolation, independent verification, and evidence.

The source repository currently describes itself as:
- v0.2 design-ready,
- v0.3 reference implementation starting.

The architecture case must therefore distinguish **designed capability** from **implemented capability**.

## Non-negotiable architecture requirements inherited from RADF

1. Humans remain accountable.
2. Models do not become the policy engine.
3. Agent actions use explicit, scoped capability.
4. Execution is constrained and disposable where practical.
5. Project/client context and credentials are isolated.
6. Important actions create durable evidence.
7. AI-generated work is untrusted until independently verified.
8. Production authorization is not implied by development capability.
9. Durable workflow state survives worker failure/restart.
10. Unauthorized actions fail closed.

## Reference validation workload

RADF uses the RithruPay prototype and scenario **REQ-1842 — Beneficiary Daily Transfer Limits** as the flagship end-to-end proof.

The scenario is useful because it crosses:
- mobile UI,
- backend API,
- database/concurrency,
- authorization,
- audit evidence,
- CI/security,
- release authorization,
- production observability.

## Architecture problem

Create a concrete deployable platform that can:

- ingest approved engineering intent,
- orchestrate durable agent workflows,
- provision isolated task runtimes,
- broker context/model/tool access,
- enforce policy before privileged actions,
- issue short-lived task credentials,
- integrate with Git/CI/security/cloud systems,
- independently verify output,
- bind approvals to exact artifacts/actions,
- retain tamper-evident evidence,
- expose operational and cost telemetry,
- recover from worker/control-plane failures.

## What this case will challenge

The RADF prototype currently proposes technologies such as Kubernetes, a durable workflow engine, OPA-compatible policy, PostgreSQL, object storage, OpenTelemetry, and ephemeral agent workers.

None of those implementation choices are accepted here merely because they appear in the source design.

Each must answer:

**Why this technology for RADF? Why not the credible alternatives?**
