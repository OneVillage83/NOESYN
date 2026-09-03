# CORE Source Contract

- **Status:** VERIFIED WITH COMMERCIAL-LICENSE GATE
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration / Access Resolution
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation / policy:**
  - https://core.ac.uk/services/api
  - https://core.ac.uk/faq
  - https://core.ac.uk/terms
  - https://api.core.ac.uk/

## NOESYN role

CORE is a valuable repository-aggregation source for:

- open-access metadata and full-text candidate discovery;
- works without DOIs;
- repository/provider provenance;
- access-location resolution;
- permitted full-text acquisition when the article/repository license supports the requested action.

CORE is **not** enabled as an unrestricted production bulk corpus by default. NOESYN is intended to become a public/commercial product, and CORE's current terms require licensing/contact for monetized products and products overlapping functions such as search/discovery/API/analytics.

## Identifiers

- Preserve CORE record/work identifiers as external aliases only.
- Preserve DOI and repository/OAI/provider identifiers independently.
- Provider/repository identity is important provenance and must not be collapsed into a generic URL.

## Operations

| Operation | Mechanism | Supported | Notes |
|---|---|---:|---|
| Search metadata | CORE API | Yes | License gate applies to production product use. |
| Fetch record | CORE API | Yes | Metadata/full-text fields vary. |
| Full-text access | CORE API / provider links | Conditional | Underlying content license controls downstream actions. |
| Repository/provider data | CORE API | Yes | Important provenance. |
| Bulk dataset | CORE Dataset | Conditional | Separate licensing/eligibility. |
| Higher-volume access | registered/VIP arrangements | Conditional | Commercial agreement may be required. |

## Authentication and rate limits

CORE currently offers API access with free/basic and registered/higher-performance modes. The public API page documents a baseline of **one batch request or five single requests per 10 seconds**, with higher rates available by arrangement.

NOESYN must provision a source-specific API key/account before production enablement and must not assume the anonymous/basic quota is appropriate for a commercial service.

## Commercial-use gate

Current CORE terms state that commercial or potentially monetized products, restricted-beneficiary products, and products overlapping CORE functions such as API/search/discovery/recommendation/analytics should contact CORE and obtain the applicable license.

Therefore:

> `CORE_PRODUCTION_LICENSE_APPROVED` defaults to false.

Until a written/applicable license is recorded:

- development may use the API only within current allowed trial/free terms;
- permanent commercial-scale ingestion is disabled;
- CORE-derived full text is not redistributed by NOESYN;
- adapter tests use permitted/minimized fixtures.

Some specifically ODC-By licensed CORE datasets have broader reuse terms, but each dataset must be classified explicitly rather than inheriting a global CORE permission.

## Pagination/freshness

Follow API v3 paging/batch semantics documented by the current API. Persist query, page/cursor/checkpoint, observation timestamp, payload hash and adapter version.

CORE continuously aggregates upstream repositories; source observations can change as repository records/full-text links are updated.

## Bulk strategy

If licensed:

1. use API for interactive/exact discovery;
2. use the appropriate CORE Dataset/FastSync or negotiated mechanism for large synchronization;
3. retain upstream repository/provider provenance;
4. do not infer article reuse rights merely from inclusion in CORE.

## Rights/license evidence

CORE aims to aggregate open-access content, but the underlying article/repository license remains the decisive evidence for downstream actions.

Preserve:

- repository/provider;
- full-text URL/source;
- article license when exposed;
- CORE dataset/API license class;
- exact version/artifact identity.

The Rights Engine separately evaluates display, persistence, TDM, embedding, summary, TTS and redistribution.

## Attribution

CORE requests attribution/citation when its API/data is used. Product attribution requirements and any organization/logo acknowledgement conditions from the applicable license must be recorded before production enablement.

## Normalized mapping

- `source = core`
- CORE record/work external ID
- observation timestamp
- raw payload hash/reference where licensed
- adapter version
- DOI/other external IDs
- title/authorship/date candidates
- repository/provider provenance
- access/full-text locations
- license observations

## Data-quality caveats

- Aggregated repository metadata can be sparse/inconsistent.
- Deduplication is provider-specific and does not replace NOESYN identity resolution.
- Full-text availability can change upstream.
- Inclusion in an OA aggregator is not proof of every derivative/reuse permission.
- commercial service/data terms are distinct from article copyright/license.

## Failure behavior

- license gate disabled: fail explicitly with `SOURCE_USE_NOT_AUTHORIZED` for gated production workflows.
- throttling: back off and reduce concurrency.
- 5xx/network: bounded retry.
- missing upstream full text: refresh access observation; do not treat as permanent deletion.
- malformed provider URL/content: route through hardened Acquisition/quarantine.

## Test fixtures

1. DOI-backed repository record;
2. non-DOI repository work;
3. record with hosted full text;
4. metadata-only record;
5. multiple repository/provider sources;
6. batch response;
7. throttle response;
8. production license-gate-disabled path.

## Contract tests

- CORE IDs remain external aliases.
- provider/repository provenance survives normalization.
- license gate blocks commercial production ingestion until approved.
- OA/full-text availability cannot bypass Rights Engine.
- repeated ingest is idempotent.

## Security notes

- API credentials are secret configuration.
- all full-text/provider URLs are untrusted and pass SSRF/redirect/content-type controls.
- retrieved documents are treated as untrusted files.

## Change history

### 2026-09-03 — v1

Initial Phase 1A contract. CORE is retained as a high-value repository/access source, but commercial production use is explicitly license-gated under the current CORE terms.