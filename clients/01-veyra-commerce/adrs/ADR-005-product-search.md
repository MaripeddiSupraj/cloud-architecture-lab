# ADR-005 — Product search platform

**Status:** Accepted  
**Date:** 2026-09-26  
**Owners:** Architecture / Catalog Engineering

## Context

The catalog contains approximately 450,000 SKUs and customer discovery requires full-text search, filtering/faceting, sorting, and relevance behavior. The transactional catalog store remains the authoritative source of product data.

## Options considered

### Amazon OpenSearch Service

**Why it fits**
- Search/index workloads can scale independently from transactional catalog storage.
- OpenSearch supports search and analytics patterns appropriate to product discovery.
- AWS manages the OpenSearch service infrastructure rather than requiring Veyra to operate the engine directly.
- A dedicated search index prevents expensive discovery queries from competing with transactional database work.

**Trade-offs**
- Introduces another data store and synchronization path.
- Index data is eventually consistent with the catalog source of truth.
- Search clusters/collections require cost, shard/index, relevance, and ingestion governance.

### PostgreSQL full-text search

**Why credible**
- Would minimize technology count and can support search for smaller/simpler catalogs.

**Why not selected**
- Product discovery requires independently scalable text search, filters/facets, relevance tuning, and query patterns that should not compete with the transactional database during sale peaks.

### Self-managed OpenSearch/Elasticsearch-compatible cluster

**Why not selected**
- Cluster lifecycle, patching, node replacement, and availability operations do not provide differentiated business value for Veyra.

### Third-party SaaS search

**Why credible**
- Can provide strong merchandising/search features and reduce platform operations.

**Why deferred**
- Introduces a commercial dependency, external data path, and pricing model that needs a separate product/business evaluation. It remains a valid future comparison if search relevance becomes a strategic differentiator.

## Decision

Use **Amazon OpenSearch Service** as the product search platform. The catalog database remains the source of truth; search is a rebuildable derived index.

The choice between provisioned OpenSearch domains and OpenSearch Serverless is intentionally deferred until query/load benchmarks and monthly cost are available.

## Synchronization principle

Catalog changes must reach the search index through a durable asynchronous path. The design must support:
- idempotent indexing,
- replay/rebuild,
- detection of indexing lag,
- full reindex without customer downtime,
- reconciliation between authoritative catalog state and search documents.

## Well-Architected impact

- **Operational Excellence:** managed engine reduces cluster operations; index health/rebuild procedures are still required.
- **Security:** search stays private behind application APIs; customers do not access the domain directly.
- **Reliability:** search failure must degrade discovery without corrupting catalog/order data.
- **Performance Efficiency:** search capacity scales independently from OLTP.
- **Cost Optimization:** avoids over-sizing the relational database for search queries; provisioned vs serverless will be cost-tested.
- **Sustainability:** derived indexes have explicit retention/rebuild policy rather than uncontrolled duplication.

## Validation

- Benchmark representative text/facet/sort queries.
- Test peak search QPS and index refresh behavior.
- Measure indexing lag.
- Prove full index rebuild.
- Compare provisioned and serverless monthly cost at normal and peak demand.

## Reference

Amazon OpenSearch Service: https://docs.aws.amazon.com/opensearch-service/latest/developerguide/
