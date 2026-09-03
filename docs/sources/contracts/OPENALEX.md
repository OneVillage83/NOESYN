# OpenAlex Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://docs.openalex.org/
  - https://docs.openalex.org/how-to-use-the-api/get-lists-of-entities/paging
  - https://docs.openalex.org/how-to-use-the-api/api-overview
  - https://docs.openalex.org/download-all-data/openalex-snapshot
  - https://openalex.org/pricing
  - https://docs.openalex.org/api-entities/works/work-object/location-object

## NOESYN role

OpenAlex is the preferred complementary broad scholarly-graph source alongside Crossref for Phase 1.

Useful for:

- broad work discovery beyond DOI-only records;
- authors, institutions, sources/venues and topic/entity candidates;
- citation/reference graph observations;
- identifier crosswalks;
- OA/access-location enrichment;
- official bulk/snapshot synchronization.

OpenAlex is **not** treated as:

- NOESYN canonical identity;
- a source whose work IDs replace DOI/PMID/arXiv identity modeling;
- proof that an OA location's content can be redistributed or transformed;
- the sole citation truth.

## Identifiers

OpenAlex entity IDs (`W...`, `A...`, `I...`, etc.) are external aliases only.

For works, preserve all exposed cross-identifiers such as DOI, PMID/PMCID and related identifiers when available. Merges/deletions in the upstream graph must not cause NOESYN canonical IDs to change.

## Operations

| Operation | Endpoint/dataset | Supported | Notes |
|---|---|---:|---|
| Search/list works | `/works` | Yes | Filter/search/query operations. |
| Fetch by OpenAlex ID | `/works/{id}` | Yes | Exact source lookup. |
| Filter by external IDs | `/works?...` | Yes | Candidate generation/crosswalk. |
| Authors/institutions/sources/topics | entity endpoints | Yes | Enrichment/candidate linking. |
| Citations/references | work graph fields/filters | Yes | Source observation, not canonical truth. |
| OA/access locations | Work/location fields | Yes | Rights evidence only. |
| Full-text links/content services | OpenAlex location/full-text products | Conditional | Original content rights remain independent. |
| Incremental updates | `from_updated_date`/API sync mechanisms | Yes | Requires reconciliation for merges/deletions. |
| Bulk snapshot | official OpenAlex snapshot | Yes | Preferred broad-scale baseline. |

## Authentication

OpenAlex supports requests without a key, but a free API key is the normal production-development path and materially increases daily allowance.

Pass the key using the documented API-key mechanism. Store it only in secret configuration; never in source control.

## Pricing/rate behavior verified for Phase 1A

Current OpenAlex API accounting is credit/cost based. Official documentation currently describes:

- single-entity lookups as free;
- list/filter calls at a lower cost tier;
- text search and semantic search at higher cost tiers;
- content downloads as separately metered;
- a much larger daily allowance with a free API key than without one;
- throttling when the daily budget is exhausted or request concurrency/rate exceeds provider limits.

Current docs also specify a 100 requests/second ceiling. Because the provider may change pricing/credits independently of schema, NOESYN must read current account limits before deployment and expose them as configuration/metrics rather than hardcoding business assumptions into adapter logic.

## Pagination

- `per_page` is capped by the provider (currently 100).
- Basic paging is not suitable past the documented deep-page limit (currently 10,000 records).
- Use cursor paging for large traversals.
- Provider OR filters have documented cardinality limits (currently up to 100 values in supported cases).

Cursor/checkpoint state must be durable and replayable.

## Freshness/update semantics

OpenAlex exposes update timestamps and supports incremental API synchronization. However, merged/deleted upstream IDs can disappear from incremental views.

Therefore NOESYN must combine:

1. incremental `updated` synchronization for routine freshness; and
2. periodic snapshot/reconciliation jobs to detect deletions, merges and upstream topology changes.

A provider merge must create source relationship observations; it must never mutate the NOESYN canonical primary key.

## Bulk/snapshot strategy

OpenAlex provides an official full snapshot of its database and is specifically suited to broad local scholarly indexing. Current public snapshot documentation describes the public snapshot as free and refreshed periodically; paid synchronization options can provide more frequent refreshes.

Phase strategy:

- Phase 1: API for deterministic adapter development, search and exact record validation.
- Scale transition: official snapshot as the baseline broad corpus, followed by incremental updates/reconciliation.

Do not design one API call per scholarly work as the permanent architecture.

## Cache/storage policy

OpenAlex metadata is distributed under CC0 according to current official documentation, making it attractive for a durable local metadata/source-observation corpus.

This **does not extend to original article PDFs/full text**. OpenAlex explicitly notes that original full-text objects retain their underlying copyrights/licenses.

## Rights/license fields

Location/license fields are important evidence, but license semantics are location-specific.

NOESYN rules:

- preserve the exact location and version attached to each license observation;
- never elevate work-level `is_oa` to a blanket reuse permission;
- treat catch-all OA labels such as provider-specific/other OA as access classification rather than broad derivative/redistribution permission;
- send location/license evidence to the Rights Engine for the requested action.

## Attribution

OpenAlex metadata is CC0, but NOESYN should retain OpenAlex provenance for transparency/debugging and follow current service/API identification requirements. Original content retains its own attribution/license requirements.

## Normalized mapping

Minimum mapping:

- `source = openalex`
- source OpenAlex entity ID
- source observation/update timestamps
- raw payload hash/reference
- adapter version
- external identifiers
- title/abstract-index candidates
- authorship/organization/venue candidates
- citation/reference observations
- version/access locations
- license/OA observations
- upstream merge/deletion observations when detected

## Data-quality caveats

- Scholarly graph records are aggregated/inferred from multiple upstreams and can disagree with publisher deposits.
- Citation graphs are incomplete and change over time.
- Author/institution disambiguation is probabilistic and must not become immutable NOESYN truth.
- OpenAlex record merges/deletions require reconciliation.
- OA status is not equivalent to broad reuse rights.
- Abstract representation may be absent or represented in provider-specific form.

## Failure behavior

- `429`: retryable after backoff/provider reset constraints.
- budget exhaustion: stop the affected source job rather than busy-retrying.
- `5xx`/network: bounded retry with jitter/circuit breaker.
- missing/merged ID: record source-state observation and attempt documented redirect/identifier reconciliation where available.
- additive schema fields: tolerate and preserve raw payload; breaking changes fail contract tests.

## Test fixtures

1. DOI-linked work with rich author/institution data;
2. non-DOI work;
3. work with PMID/PMCID cross-identifiers;
4. multiple locations with differing OA/license evidence;
5. citations/references;
6. cursor page;
7. merged/deleted-ID reconciliation case;
8. sparse work;
9. 429/budget exhaustion behavior;
10. unknown additive field.

## Contract tests

- OpenAlex ID never becomes canonical NOESYN ID.
- Repeated ingest is idempotent.
- changed upstream payload produces a new observation.
- multiple location licenses remain attached to their locations/versions.
- `is_oa` alone cannot authorize storage/TTS/redistribution.
- cursor sync replays from durable checkpoints.
- reconciliation can represent an upstream merge without changing canonical ID.

## Security notes

- Treat all provider URLs/text as untrusted.
- Route full-text URL retrieval through the hardened Acquisition service.
- API keys remain secret configuration.
- Never automatically follow arbitrary redirects into private networks.

## Change history

### 2026-09-03 — v1

Initial verified contract. Phase 1A designates OpenAlex as the preferred broad graph/metadata complement to Crossref, subject to the architecture ADR.