# ADR-020 — Cognito feature tier

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Security Architecture / Product / FinOps

## Context

Veyra plans for approximately 600,000 monthly active customers. At this scale, the Amazon Cognito feature tier is a material architecture-cost decision rather than a minor identity setting.

The launch requirements need:
- username/password authentication,
- optional social/OIDC federation,
- standards-based tokens,
- MFA using authenticator apps and/or SMS where policy requires it,
- account recovery and normal user-pool lifecycle features.

The current requirements do **not** require passwordless one-time-code login, passkeys, the visual managed-login editor, or Cognito threat-protection/adaptive-authentication features.

## Options considered

### Cognito Lite

**Why it fits**
- Supports username/password sign-in.
- Supports social, SAML, and OIDC federation.
- Supports authenticator-app and SMS MFA.
- Supports Lambda-based custom runtime actions.
- Meets the current launch authentication requirement without paying for unused higher-tier features.

**Cost sensitivity at 600k direct/social MAU**

Using the current Cognito Lite tiers:
- first 10k MAU: free,
- next 90k: $0.0055/MAU,
- remaining 500k: $0.0046/MAU.

Planning cost:

~~~
90,000 × 0.0055 = $495
500,000 × 0.0046 = $2,300
Total ≈ $2,795/month
~~~

SMS/email delivery remains separately billable.

### Cognito Essentials

**Why credible**
- Adds passwordless sign-in, passkeys, email OTP MFA, richer managed-login customization, and other authentication UX features.

**Why not selected now**
- None of those capabilities is currently a launch requirement.
- Current pricing above the 10k free tier is $0.015 per MAU.

At 600k MAU:

~~~
590,000 × $0.015 ≈ $8,850/month
~~~

That is roughly $6,055/month more than Lite before SMS/email charges.

### Cognito Plus

**Why credible**
- Adds elevated threat-protection capabilities such as adaptive/risk-oriented protections.

**Why not selected now**
- Current architecture requirements have not established that Cognito's advanced threat-protection capability is mandatory.
- Plus has no MAU free tier and is priced at $0.020/MAU in the current pricing example.

At 600k MAU:

~~~
600,000 × $0.020 ≈ $12,000/month
~~~

This tier should be chosen because of a security requirement, not because a higher tier sounds more secure.

## Decision

Use **Amazon Cognito Lite** for the launch architecture.

Security controls still include:
- MFA policy where required,
- WAF/rate/bot controls,
- authentication telemetry and alerting,
- account recovery controls,
- secure token validation,
- fraud controls in the commerce domain where identity signals alone are insufficient.

## Upgrade triggers

Reconsider Essentials or Plus if:
- passwordless or passkeys become a product requirement,
- email OTP MFA is required,
- richer managed-login tooling has measurable product value,
- threat modelling identifies adaptive authentication/compromised-credential protection as a required control,
- regulatory or contractual requirements demand those controls.

## Well-Architected impact

- **Operational Excellence:** managed identity remains in place.
- **Security:** required launch identity/MFA capabilities are retained.
- **Reliability:** same managed user-pool SLA model.
- **Performance Efficiency:** no additional application-hosted auth infrastructure.
- **Cost Optimization:** avoids paying roughly 3x–4x identity MAU pricing for unused launch features.
- **Sustainability:** uses only the platform capability required by the workload.

## References

- Cognito pricing: https://aws.amazon.com/cognito/pricing/
- Cognito feature plans: https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-sign-in-feature-plans.html
