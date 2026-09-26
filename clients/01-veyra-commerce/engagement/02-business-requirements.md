# Business Requirements

## Customer journeys

The platform must support:

- Customer registration and authentication.
- Product browse, category navigation, filtering, and search.
- Product detail and availability views.
- Shopping cart persistence across supported channels.
- Promotion and price evaluation.
- Checkout and address selection.
- External payment initiation and confirmation.
- Order creation and customer-visible order status.
- Shipment tracking through fulfilment integrations.
- Cancellation, return, and refund initiation.
- Customer notifications for material order events.

## Commerce capabilities

The platform must provide controlled management of:

- Product catalog and attributes.
- Price and promotion rules.
- Inventory availability.
- Orders and order lifecycle.
- Customer profiles and preferences.
- Payment state and reconciliation references.
- Returns and refunds.
- Search indexing.
- Operational administration.

## Business scale baseline

Architecture planning will initially use the following approved assumptions:

| Measure | Baseline |
|---|---:|
| Registered customers | 2.5 million |
| Monthly active customers | 600,000 |
| Daily active customers | 120,000 |
| Catalog size | 450,000 SKUs |
| Normal orders/day | 35,000 |
| Peak-event orders/day | 150,000 |
| Existing product/media footprint | ~8 TB |
| Existing transactional data | ~1.2 TB |

These numbers are planning inputs, not capacity guarantees. Sensitivity analysis will be performed for materially higher traffic.

## Growth requirement

The design should accommodate growth to approximately 5 million registered customers without a fundamental re-platforming exercise.

The architecture should not assume multi-region active-active operation at launch unless a documented requirement justifies it.

## External dependencies

The platform must integrate with:

- Payment service provider(s).
- Tax and invoicing services where required.
- Fulfilment/warehouse systems.
- Courier or shipment-tracking providers.
- Email, SMS, and push-notification providers.
- Existing enterprise systems that remain systems of record during migration.

Each dependency will require timeout, retry, idempotency, reconciliation, and failure-isolation behaviour to be defined during detailed design.
