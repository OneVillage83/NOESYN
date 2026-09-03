# NOESYN Scholarly Source Strategy

## Goal

NOESYN should discover broadly without treating any single external provider as canonical truth.

The source layer exists to collect observations. Canonicalization, version resolution, access selection, and rights decisions happen in NOESYN-owned domains.

## Source classes

### Metadata and identity sources

Primary candidates:

- Crossref
- OpenAlex
- Semantic Scholar Academic Graph
- PubMed
- Europe PMC
- arXiv
- DOAJ
- DataCite where relevant to research objects

### Open/full-text and repository sources

Primary candidates:

- PubMed Central
- Europe PMC
- CORE
- arXiv
- institutional repositories
- subject repositories
- publisher-hosted open content

### Access-resolution sources

Primary candidates:

- Unpaywall
- CORE
- repository metadata
- publisher metadata
- source-specific OA fields

### Citation/relationship sources

Primary candidates:

- Semantic Scholar
- OpenAlex
- Crossref relationship/update metadata
- PubMed/PMC where available
- parsed document references

No citation graph source is assumed complete.

## Adapter contract

Every adapter must document:

```text
SourceAdapterContract
├─ source name / version
├─ supported operations
├─ authentication model
├─ rate / concurrency limits
├─ bulk-access options
├─ attribution requirements
├─ caching rules
├─ retry/backoff policy
├─ pagination semantics
├─ update/freshness semantics
├─ identifier namespaces
├─ metadata field mapping
├─ full-text capabilities
├─ rights/license signals
├─ known data-quality caveats
├─ terms/policy references
└─ adapter test fixtures
```

## Standard adapter operations

Not every source implements every operation, but the internal interfaces should be consistent:

- `search_works(query, filters, cursor)`
- `get_work_by_external_id(namespace, value)`
- `get_author(...)`
- `get_citations(...)`
- `get_references(...)`
- `get_updates_since(...)`
- `resolve_access_locations(...)`
- `retrieve_artifact(...)`
- `get_rights_metadata(...)`

Unsupported operations must fail explicitly rather than returning misleading empty results.

## Initial source roles

### Crossref

Primary role:

- DOI-centric metadata backbone;
- publisher-deposited bibliographic metadata;
- funding, ORCID/ROR links when deposited;
- licenses and post-publication updates when available.

Important current operational facts:

- REST API is publicly available;
- identified/polite access is recommended;
- cache responses and back off responsibly;
- public data files/snapshots are available for bulk-scale use.

Official docs:
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/access-and-authentication/
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/tips-for-using-public-data-files-and-plus-snapshots/

### OpenAlex

Primary role:

- broad scholarly graph;
- works/authors/institutions/topics/venues;
- citation and OA-related enrichment;
- candidate generation and entity linking.

Architecture rule: OpenAlex IDs are external aliases, never NOESYN primary keys.

Before implementation, freeze the current API/snapshot contract from official documentation and record rate/auth/bulk terms in a dedicated source contract.

### Semantic Scholar

Primary role:

- paper/author/citation/reference graph;
- recommendations/semantic discovery;
- optional embeddings/features where terms permit;
- downloadable datasets for scale where appropriate.

Official API families currently include Academic Graph, Recommendations, and Datasets.

Official docs:
- https://www.semanticscholar.org/product/api
- https://api.semanticscholar.org/api-docs/

### Unpaywall

Primary role:

- DOI-based OA status and candidate access locations;
- access resolution, not canonical identity.

Current API v2 requires an email parameter. Published guidance limits API use to 100,000 calls/day and recommends the database snapshot for higher-scale local use.

Official docs:
- https://unpaywall.org/products/api

### CORE

Primary role:

- repository aggregation;
- OA metadata and full-text candidate discovery;
- repository/provider linkage.

CORE exposes machine-readable metadata and full text, with API terms and rate policies that must be captured before production-scale use.

Official docs:
- https://core.ac.uk/services/api

### PubMed

Primary role:

- biomedical bibliographic discovery;
- PMID identity;
- MeSH and biomedical-specific metadata;
- linkage to PMC and NCBI ecosystem.

Implementation should use approved NCBI programmatic interfaces and respect NCBI usage policies.

### PubMed Central (PMC)

Primary role:

- biomedical structured/open full text where permitted;
- PMCID identity;
- JATS/XML-rich document ingestion;
- author-manuscript and OA datasets according to collection terms.

Important: presence in PMC does not mean unrestricted reuse. PMC requires automated retrieval through approved developer services and article-level license compliance.

Official docs:
- https://pmc.ncbi.nlm.nih.gov/tools/developers/
- https://pmc.ncbi.nlm.nih.gov/tools/oai/

### Europe PMC

Primary role:

- life-sciences discovery;
- metadata, citations/related information, grants, annotations;
- OA full-text access and bulk/OAI options.

Official docs:
- https://europepmc.org/developers

### arXiv

Primary role:

- preprint discovery and versioning in supported fields;
- arXiv identifier aliases;
- source/PDF access according to arXiv terms/licenses;
- early research versions important for version graphs.

Before implementation, capture current API/bulk-access and content-license rules in a dedicated source contract. Do not assume every arXiv submission uses the same reuse license.

### DOAJ

Primary role:

- OA journal/article discovery;
- journal-level OA metadata and candidate license evidence.

Before implementation, freeze current API terms/schema and distinguish journal policy from article-level rights evidence.

## Federated search strategy

### Stage 1 — query understanding

Normalize:

- free-text terms;
- exact title/author/DOI requests;
- date ranges;
- fields/disciplines;
- publication types;
- OA preference;
- citation/author constraints.

### Stage 2 — candidate generation

Query multiple relevant sources in parallel, subject to rate and availability policy.

### Stage 3 — candidate normalization

Map source records into a provider-neutral observation schema.

### Stage 4 — identity resolution

Resolve candidates into canonical NOESYN works/versions using identifiers plus probabilistic/heuristic matching.

### Stage 5 — ranking

Ranking should combine:

- lexical/semantic relevance;
- field/date intent;
- source agreement;
- document type;
- version state;
- integrity flags;
- availability;
- user context.

Citation count may be a feature but never a truth score.

## Deduplication signals

High-confidence signals:

- normalized DOI match;
- PMID/PMCID crosswalk;
- exact arXiv-version relationship;
- trusted provider cross-identifiers.

Secondary signals:

- normalized title similarity;
- author overlap/order;
- publication date proximity;
- venue;
- abstract similarity;
- reference fingerprints;
- document hashes.

Ambiguous matches should remain unresolved rather than forced.

## Freshness and replay

Each source observation must record:

- source;
- source record ID;
- observation timestamp;
- source update timestamp where available;
- adapter version;
- payload/normalized hash.

Incremental synchronization must not destroy prior observations that were used by historical derived outputs.

## Source-quality policy

NOESYN should eventually track quality dimensions such as:

- identifier completeness;
- title/author disagreement;
- abstract availability;
- citation disagreement;
- access-location freshness;
- license evidence quality;
- retraction/update timeliness.

Provider disagreement should be surfaced to identity/policy systems rather than silently resolved by fixed source precedence.

## Scaling strategy

Start with APIs for controlled development and correctness. For providers offering official bulk datasets/snapshots, transition high-volume indexing to bulk/incremental workflows when scale justifies it.

Avoid designing a system that requires one API call per paper forever.

## Phase 0 unresolved source work

Before any adapter becomes production-ready, create a dedicated source contract under `docs/sources/contracts/` containing:

- exact current endpoints;
- API/schema version;
- auth requirements;
- rate limits;
- bulk options;
- terms/licensing URLs;
- allowed caching/storage;
- attribution;
- fixtures;
- known edge cases;
- date last verified.

This source-contract freeze is part of Phase 1 implementation readiness.
