# Semantic Scholar Source Contract

- **Status:** VERIFIED WITH COMMERCIAL-USE GATE
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://www.semanticscholar.org/product/api
  - https://api.semanticscholar.org/api-docs/
  - https://www.semanticscholar.org/product/api/tutorial
  - https://www.semanticscholar.org/product/api/license

## NOESYN role

Semantic Scholar is a high-value enrichment and research-discovery source for:

- paper/author graph observations;
- references and citations;
- recommendations;
- semantic discovery;
- source-specific enrichment fields where licensed for the intended use.

For Phase 1 it is **not** the primary local scholarly metadata backbone. Crossref + OpenAlex are preferred for the initial durable broad metadata corpus because their present access/reuse model is clearer for the planned public/commercial product.

Semantic Scholar remains strategically important, but dataset/API use must pass the current applicable AI2 license/terms gate for NOESYN's actual deployment mode.

## Identifiers

- Semantic Scholar paper and author IDs are external aliases.
- API lookups can use supported external identifiers such as DOI/arXiv/PMID where documented.
- Preserve S2 IDs for graph reconciliation but never expose them as NOESYN canonical primary keys.

## API families

Current official families:

- Academic Graph API — `https://api.semanticscholar.org/graph/v1`
- Recommendations API — `https://api.semanticscholar.org/recommendations/v1`
- Datasets API — `https://api.semanticscholar.org/datasets/v1`

## Operations

| Operation | API | Supported | Notes |
|---|---|---:|---|
| Search papers | Academic Graph | Yes | Field selection required/recommended. |
| Fetch paper by ID | Academic Graph | Yes | Supports provider/external identifiers. |
| Batch paper lookup | Academic Graph | Yes | Preferred for efficiency. |
| Authors | Academic Graph | Yes | Enrichment/graph source. |
| Citations/references | Academic Graph | Yes | Observation, not canonical truth. |
| Bulk paper search | Academic Graph bulk search | Yes | Token pagination. |
| Recommendations | Recommendations API | Yes | Rebuildable derived discovery input. |
| Dataset releases | Datasets API | Conditional | License gate required before corpus ingestion. |
| Full text | Not a general authorization source | No/Conditional | PDF URLs/fields do not grant reuse rights. |

## Authentication and rate behavior

The API can be used in a shared unauthenticated pool for some operations, but official guidance recommends an API key for stable use.

Current introductory key guidance documents roughly 1 request/second for individual API keys and recommends batch endpoints for efficiency. Actual runtime response headers/provider limits remain authoritative.

NOESYN must:

- configure per-source token-bucket throttling;
- prefer batch operations;
- back off on 429s;
- avoid treating the shared unauthenticated pool as production capacity.

## Pagination

- Bulk search uses continuation/token pagination.
- Persist token/checkpoint and normalized query definition for replay.
- Request only fields required by the adapter contract to reduce payload/cost.

## Dataset and licensing gate

This is the critical contract constraint.

Semantic Scholar API/data licensing distinguishes the API service from underlying S2 data and third-party content. Official license materials place conditions on use of API/data, require attribution, and may impose non-commercial/internal research limits unless expanded rights are obtained.

Therefore:

> **NOESYN MUST NOT import Semantic Scholar downloadable datasets into a public/commercial production corpus until the exact applicable current AI2 license is reviewed and documented for that intended use.**

The adapter may be developed against allowed API usage and fixtures, but production enablement is controlled by a feature/license gate.

## Cache/storage policy

- Cache only to the extent allowed by the applicable current API/data agreement.
- Retain S2 provenance on observations.
- Do not assume third-party abstracts/full-text/embeddings inherit a broad Semantic Scholar reuse license.
- Before long-lived local persistence of S2 dataset fields, document the exact governing license in this contract.

## Rights fields

Any open-access/PDF URL or related field is discovery evidence only. It does not authorize:

- fetching protected content;
- permanent storage;
- redistribution;
- TTS;
- commercial derivative generation.

Resolve access and rights independently.

## Attribution

Current API/data agreements require Semantic Scholar attribution. Product-facing attribution requirements must be rechecked at implementation and represented in source configuration so UI/API outputs can satisfy them.

## Normalized mapping

Minimum mapping when enabled:

- `source = semantic_scholar`
- S2 paper/author ID
- observation timestamp
- raw payload hash/reference if retention permitted
- adapter version
- external identifiers
- metadata candidates
- citation/reference observations
- recommendation/semantic scores only as rebuildable source features
- licensing class of each persisted field/dataset where required

## Data-quality caveats

- Citation/reference coverage is incomplete and can disagree with OpenAlex/Crossref/document parsing.
- Author disambiguation is source-specific.
- Abstract/PDF availability is not universal.
- Citation influence or recommendation scores are not evidence quality scores.
- Dataset/API fields and terms can evolve independently.

## Failure behavior

- 429/shared-pool saturation: retry with provider-safe backoff; authenticated production calls should not fall back silently to an uncontrolled shared pool.
- 4xx invalid ID/query: non-retryable until input changes.
- 5xx/network: bounded retries.
- license gate disabled: fail explicitly with `SOURCE_USE_NOT_AUTHORIZED`, not an empty result.

## Test fixtures

1. DOI-linked paper;
2. arXiv/PMID-linked paper;
3. citations + references;
4. batch lookup;
5. token-paginated bulk search;
6. sparse paper;
7. 429 response;
8. license-gate-disabled production path;
9. additive unknown field.

## Contract tests

- S2 IDs remain external aliases.
- citation edges include source provenance.
- recommendation/ranking features cannot become evidence truth.
- disabled license gate blocks disallowed persistent/bulk workflows.
- batch ingest is idempotent.
- unknown fields are tolerated without silent semantic remapping.

## Security notes

- API key is secret configuration.
- URLs returned by the provider are untrusted and must pass acquisition policy.
- Do not send private user documents/content to Semantic Scholar APIs unless a future contract explicitly permits and requires it.

## Change history

### 2026-09-03 — v1

Initial Phase 1A contract. Semantic Scholar is retained as a strategically valuable enrichment/recommendation/citation source, but broad production dataset use is gated pending explicit commercial/public-product license clearance.