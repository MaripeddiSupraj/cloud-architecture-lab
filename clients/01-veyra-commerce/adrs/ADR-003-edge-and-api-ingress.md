# ADR-003 — Edge delivery and API ingress

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Platform / Security

## Context

Veyra serves web and mobile customers in India. Customer traffic is bursty, promotional events may reach approximately 15,000 RPS, and the public platform needs DDoS/web-threat protection, static/media acceleration, TLS termination, and reliable routing to container workloads.

## Options considered

### CloudFront + AWS WAF + Application Load Balancer

**Why it fits**
- CloudFront provides an edge layer for cacheable web/static/media content and reduces avoidable origin traffic.
- AWS WAF provides centrally managed web request filtering at the public edge.
- Application Load Balancer maps naturally to long-running ECS HTTP services and supports path/host routing.
- The pattern separates internet/edge concerns from application-service routing.

**Trade-offs**
- Two routing layers must be configured and observed correctly.
- Dynamic APIs still reach the origin; CloudFront does not remove the need to scale application capacity.
- Origin bypass protection must be part of detailed network/security design.

### Amazon API Gateway

**Why credible**
- Strong fit when the platform needs API-product management, usage plans, per-client throttles, request transformation, WebSocket APIs, or strongly serverless backends.

**Why not selected as the default customer API ingress**
- Initial traffic is primarily Veyra-owned web/mobile clients calling container services.
- Current requirements do not include external developer API products or per-consumer API monetization.
- Adding API Gateway in front of every container API would introduce another request-processing and cost layer without a demonstrated requirement.

API Gateway remains a valid later choice for partner/public APIs with different governance needs.

### Direct public ALB

**Why credible**
- Simple routing to ECS services.

**Why not selected**
- It would give up the edge caching/origin shielding and centralized public-edge protection valuable for a consumer commerce site.
- Media/static traffic should not consume application-origin capacity.

## Decision

Use **Amazon CloudFront + AWS WAF** as the public edge and **Application Load Balancer** as the primary ingress to ECS/Fargate application services.

Static web assets and product media are designed for cacheable edge delivery. Dynamic API cache behavior will be explicit and conservative to avoid caching personalized or transactional responses incorrectly.

## Well-Architected impact

- **Operational Excellence:** clear edge and origin responsibility with centralized request telemetry.
- **Security:** public traffic is filtered before reaching application subnets; origin-bypass controls will be mandatory.
- **Reliability:** cached content reduces dependency on origin availability and protects against traffic spikes.
- **Performance Efficiency:** edge delivery lowers latency and origin work for cacheable content.
- **Cost Optimization:** cache hit ratio can reduce origin compute and data processing; CloudFront/WAF request cost must be modelled.
- **Sustainability:** fewer repeated origin computations/transfers for cacheable assets.

## Validation

- Measure cache-hit ratio for web/media traffic.
- Load test dynamic API paths through the complete edge chain.
- Test WAF false-positive/false-negative behavior against representative traffic.
- Validate the ALB origin cannot be used to bypass required edge controls.

## Revisit triggers

- External/partner API products require per-client plans/keys/quotas.
- WebSocket or protocol requirements change.
- API transformation/governance requirements exceed ALB capabilities.
