# ADR-0004: Crossref + OpenAlex Initial Corpus and Hybrid Discovery

- **Status:** Accepted
- **Date:** 2026-09-03
- **Decision owners:** NOESYN project
- **Scope:** Initial universal metadata sources and search/discovery synchronization strategy

## Context

NOESYN needs broad scholarly discovery without depending forever on live third-party requests and without treating a single provider graph as canonical truth.

The initial source pair should support:

- DOI identity and publisher-deposited metadata;
- broad non-DOI candidate generation;
- citation/author/institution enrichment;
- official bulk paths for future scale;
- terms suitable for a public/commercial metadata product;
- reproducible local source observations.

## Decision

### Initial universal metadata pair

Use **Crossref + OpenAlex**.

- Crossref is the initial DOI-centric/publisher-deposit backbone.
- OpenAlex is the initial broad graph/candidate/enrichment complement.
- Both remain source observations; neither provider's ID is a NOESYN canonical primary key.

### Biomedical augmentation

Use PubMed, PMC and Europe PMC as domain-specific enrichments under their dedicated contracts.

### Access resolution

Use Unpaywall as a focused DOI OA/access-location resolver. For broad new OA synchronization, align with the current OpenAlex snapshot/sync model rather than assuming the legacy standalone Unpaywall snapshot model.

### Deferred/gated sources

- Semantic Scholar remains high-value for citations/recommendations/semantic enrichment but broad persistent commercial dataset use stays behind its source-license gate.
- CORE remains high-value for repository/full-text discovery but commercial product integration stays behind its current CORE license gate.
- arXiv is integrated as a version-aware preprint source, not a universal metadata backbone.

### Search/discovery strategy

Use a **hybrid local-first strategy**:

1. search the locally synchronized/canonical corpus first;
2. use permitted external APIs for candidate expansion and freshness;
3. normalize/canonicalize/deduplicate before durable insertion/presentation as a NOESYN work;
4. progressively adopt official provider snapshots/bulk data for corpus-scale baseline sync;
5. never require one external API call per user search as the permanent design.

PostgreSQL full-text search is the initial local metadata search projection. Dedicated search infrastructure is deferred until measured relevance/scale needs justify it.

## Why not Crossref alone

Crossref is excellent for DOI-centric metadata but cannot represent the full scholarly universe, particularly non-DOI outputs and broad graph enrichment.

## Why OpenAlex over Semantic Scholar as the initial complement

Current OpenAlex metadata is available under CC0 and has an official broad snapshot/sync path. Semantic Scholar is strategically valuable, but current AI2 API/data licensing requires a more explicit commercial/public-product use gate for broad persistent ingestion.

## Consequences

### Positive

- strong DOI exact-identity observations plus broad graph coverage;
- clear large-scale snapshot path;
- commercial metadata foundation does not depend on a still-unresolved dataset license;
- external-source outages degrade freshness/expansion rather than taking down all local search;
- source disagreement remains visible to NOESYN identity logic.

### Costs

- source deduplication/canonicalization is required from the first slice;
- local corpus synchronization/storage work arrives earlier than a purely federated MVP;
- provider merge/deletion reconciliation must be implemented.

## Review triggers

Review if provider terms, data quality or availability materially change, or if measured discovery recall shows another source should join the baseline pair. Adding a source does not require replacing NOESYN canonical identity.