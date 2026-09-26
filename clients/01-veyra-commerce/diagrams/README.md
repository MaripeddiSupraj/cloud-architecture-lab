# Eraser Diagram Set

The architecture is intentionally represented by multiple diagrams. A single "everything diagram" becomes hard to review, hard to maintain, and hides important failure/security paths.

## Diagram hierarchy

| Diagram | Audience / question answered |
|---|---|
| [00 — System context](00-system-context.eraserdiagram) | What is Veyra, who uses it, and what external systems surround it? |
| [01 — AWS platform HLD](01-initial-aws-platform-hld.eraserdiagram) | What are the major AWS building blocks and service boundaries? |
| [02 — Network & security](02-network-security.eraserdiagram) | Where are public/private/data boundaries and how is traffic controlled? |
| [03 — Checkout/payment](03-checkout-payment.eraserdiagram) | How does a successful checkout preserve correctness? |
| [04 — Payment uncertainty recovery](04-payment-uncertainty-recovery.eraserdiagram) | What happens if payment succeeds but the request/callback fails? |
| [05 — Data & events](05-data-and-events.eraserdiagram) | Which system owns truth and how are derived/async views updated? |
| [06 — CI/CD & release](06-cicd-release.eraserdiagram) | How does code safely become production? |
| [07 — Observability & operations](07-observability-operations.eraserdiagram) | How do teams detect customer impact and diagnose failures? |
| [08 — Disaster recovery](08-disaster-recovery.eraserdiagram) | How does the platform recover from a Region-level outage? |

## Visual rules

- One diagram answers one main question.
- Customer traffic flows left-to-right.
- Public/edge, application, data, and integration boundaries are visually separated.
- Authoritative stores and derived stores are labelled explicitly.
- Async paths are labelled as async.
- External providers are outside the AWS/VPC boundary.
- A diagram may only show a technology decision once its ADR is accepted.
- Detailed diagrams may omit unrelated components to keep the review readable.
- Eraser source is version-controlled; exported images are secondary artifacts.

## Why multiple diagrams?

The executive HLD should remain understandable in under a minute. Checkout correctness, DR, networking, and CI/CD require different levels of detail and different audiences. Keeping them separate prevents a "spaghetti diagram" while preserving traceability back to the same architecture.
