# Discovery Question Register

This register captures information that can materially change architecture or cost. Items remain open until confirmed by a business, product, security, operations, or engineering owner.

| ID | Area | Question | Why it matters | Status |
|---|---|---|---|---|
| DQ-001 | Traffic | What percentage of peak traffic is authenticated versus anonymous browse? | Cacheability, origin load, session design | Open |
| DQ-002 | Sales events | What is the steepest expected traffic ramp: minutes or seconds? | Autoscaling headroom and queue/backpressure design | Open |
| DQ-003 | Orders | What percentage of peak-event orders arrive in the busiest 5-minute window? | Transaction and inventory capacity | Open |
| DQ-004 | Inventory | Is stock authoritative in Veyra or an external ERP/WMS? | Consistency boundary and reservation pattern | Open |
| DQ-005 | Inventory | Is oversell ever acceptable for specific product classes? | Consistency and concurrency controls | Open |
| DQ-006 | Payments | Which payment providers are launch dependencies and what are their timeout/SLA characteristics? | Checkout resilience and reconciliation | Open |
| DQ-007 | Payments | Can payment confirmation arrive asynchronously after the customer request ends? | Workflow and state-machine design | Open |
| DQ-008 | Catalog | What is the maximum attribute cardinality and update frequency per SKU? | Data and search model | Open |
| DQ-009 | Pricing | Are prices personalized or customer-segment dependent? | Cache strategy and pricing-service design | Open |
| DQ-010 | Promotions | Can multiple promotions stack and require transactional validation at checkout? | Latency and correctness | Open |
| DQ-011 | Cart | Required cart retention after last customer activity? | Storage and TTL | Open |
| DQ-012 | Identity | Existing customer identity provider or migration requirement? | Authentication architecture | Open |
| DQ-013 | Compliance | Required data residency, retention, deletion, and audit obligations? | Region, encryption, backup, data lifecycle | Open |
| DQ-014 | DR | Is region-level failure in the formal launch SLA? | Multi-region design and cost | Open |
| DQ-015 | Operations | Is 24x7 on-call available at launch? | Operational design and managed-service bias | Open |
| DQ-016 | Delivery | Current release frequency and target release frequency? | CI/CD and deployment strategy | Open |
| DQ-017 | Migration | Must customer sessions/carts survive cutover from the legacy platform? | Migration and coexistence design | Open |
| DQ-018 | Analytics | What events require near-real-time consumption versus batch processing? | Streaming requirement | Open |
| DQ-019 | Security | Required external standards or contractual controls beyond payment scope? | Security control baseline | Open |
| DQ-020 | Cost | Does the stated run-rate include observability, CDN egress, security services, and DR? | FinOps baseline | Open |

## Rule

A design decision may proceed with an assumption when necessary, but the assumption must be recorded and the impact of a different answer must be understood.
