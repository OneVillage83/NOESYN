# Crossref Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://www.crossref.org/documentation/retrieve-metadata/rest-api/
  - https://www.crossref.org/documentation/retrieve-metadata/rest-api/access-and-authentication/
  - https://www.crossref.org/documentation/retrieve-metadata/rest-api/tips-for-using-public-data-files-and-plus-snapshots/
  - https://www.crossref.org/blog/update-to-rest-api-rate-limits/
- **Base REST URL:** `https://api.crossref.org/`

## NOESYN role

Crossref is NOESYN's initial DOI-centric metadata backbone and a high-confidence source of publisher/member-deposited bibliographic observations.

Useful for:

- DOI identity candidate generation;
- titles, contributors, publication/registration dates, containers and types;
- references and relationships when deposited;
- funding metadata;
- ORCID/ROR links when deposited;
- license links and post-publication update metadata when deposited.

Crossref is **not** treated as:

- the sole canonical truth for a scholarly work;
- proof that a linked full-text resource is lawfully accessible to the current user;
- proof that an article may be stored, redistributed, summarized or narrated;
- a complete abstract, citation or relationship source.

## Identifiers

- Primary provider identity signal: DOI.
- Normalize DOI before matching: trim, lowercase ASCII where appropriate, remove resolver prefixes such as `https://doi.org/` and `doi:` while preserving the normalized DOI string.
- Crossref metadata observations must retain the original provider payload and observation timestamp.
- DOI is a durable external identifier/alias, not the NOESYN primary key.

## Operations

| Operation | Endpoint/dataset | Supported | Notes |
|---|---|---:|---|
| Search/list works | `/works` | Yes | Filters, queries, select/sample where applicable. |
| Fetch by DOI | `/works/{doi}` | Yes | Preferred exact DOI lookup. |
| Members/prefixes/funders/journals | REST resource routes | Yes | Enrichment/validation only. |
| References | work record | Conditional | Only when deposited/available. |
| Access/full-text links | work record `link` data | Conditional | A link does not establish access or reuse rights. |
| License observations | work record license metadata | Conditional | Evidence input to Rights Engine, not a final rights decision. |
| Incremental synchronization | filters/cursors + source timestamps | Yes | Adapter must preserve observations and use replay-safe checkpoints. |
| Bulk snapshot | public data file / Metadata Plus snapshot | Yes | Preferred at large corpus scale. |

## Authentication and identification

Crossref public REST metadata is accessible without signup. NOESYN should use identified/polite access by sending a valid `mailto` parameter and a descriptive `User-Agent` containing contact information.

Metadata Plus/API-token access is a separate commercial service and is not required for Phase 1.

## Rate and concurrency limits

Crossref changed public REST limits effective 2025-12-01. As verified for this contract:

- Public, single-record requests: up to 5 requests/second, concurrency 1.
- Public, list/query requests: up to 1 request/second, concurrency 1.
- Polite, single-record requests: up to 10 requests/second, concurrency 3.
- Polite, list/query requests: up to 3 requests/second, concurrency 3.
- Metadata Plus limits are separately provisioned.

The adapter must still treat response headers as authoritative at runtime, back off on throttling, jitter retries and avoid traffic spikes.

## Pagination

- Use cursor-based pagination for deep result traversal.
- Do not build large corpus synchronization around offset paging.
- Persist the query/filter definition, cursor checkpoint, adapter version and observation window for replayability.

## Freshness and update semantics

Crossref metadata can change after initial registration. NOESYN must store each material source observation with:

- retrieval timestamp;
- source update/index timestamp when exposed;
- adapter version;
- normalized payload hash;
- raw-payload object/hash reference where retained.

Updates should produce new observations rather than destructively overwriting historical evidence used by derived artifacts.

## Bulk/snapshot strategy

API use is appropriate for controlled development, exact lookups and incremental discovery. Crossref explicitly recommends public data files/snapshots rather than hundreds of thousands or millions of REST calls.

NOESYN scaling rule:

1. REST for Phase 1 correctness and incremental exact lookup.
2. Cache stable responses.
3. Migrate high-volume baseline ingest to official public data files or an appropriate Crossref snapshot product when scale justifies it.

## Cache/storage policy

Crossref bibliographic metadata is generally designed for reuse, but individual fields such as abstracts can carry separate copyright considerations. NOESYN must preserve field/source provenance and must not let a general metadata-reuse assumption authorize full-text or derivative actions.

## Rights/license fields

Crossref license and link metadata are **rights evidence only**.

They do not prove:

- that a URL is open to the current user;
- that the linked resource is the same WorkVersion NOESYN is processing;
- that text mining, persistence, redistribution, TTS or commercial derivative use is permitted.

A Crossref full-text/TDM link can still require publisher authentication or a separate license.

## Attribution and service etiquette

- Identify NOESYN requests with contact details.
- Cache responsibly.
- Prefer bulk datasets at large scale.
- Retain Crossref as the provenance source for observations derived from it.

## Normalized mapping

Minimum `SourceRecordObservation` mapping:

- `source = crossref`
- `source_record_id = normalized DOI`
- `observed_at`
- source timestamps when available
- raw payload hash/reference
- adapter version
- title candidates
- contributor candidates
- DOI external identifier
- publication/container/type candidates
- relationship/reference observations
- license/access-link observations

Canonicalization happens later in NOESYN Identity.

## Data-quality caveats

- Deposit completeness varies by publisher/member and field.
- Abstracts, references, ORCID/ROR, funding and licenses may be absent.
- Dates can represent different publication/registration events and must not be collapsed into one generic date.
- Link presence is not access permission.
- Duplicate/version relationships require cross-source resolution.

## Failure behavior

- `429`: retryable after provider-directed/backoff delay.
- `5xx`/network failure: bounded retry with jitter and circuit-breaker behavior.
- `4xx` invalid query/identifier: generally non-retryable until input changes.
- Schema drift: quarantine the observation and fail the contract test rather than silently dropping fields.

## Test fixtures

Phase 1 adapter fixtures must include:

1. exact DOI lookup with rich metadata;
2. sparse DOI record;
3. record with license/link metadata;
4. record with updates/relationships;
5. cursor pagination response;
6. 404/not-found;
7. 429 throttling response;
8. unknown additive field to prove forward-compatible parsing.

Fixtures should be stored as legally reusable metadata payloads or minimized synthetic equivalents.

## Contract tests

- DOI normalization is deterministic.
- Re-ingesting the same payload is idempotent.
- Changed payload creates a new source observation when material.
- Unknown fields do not corrupt normalized output.
- Access links never directly set an action permission to `ALLOW`.
- Cursor checkpoints replay deterministically.

## Security notes

- Treat all URLs and text fields as untrusted input.
- Never automatically fetch arbitrary Crossref-returned URLs without acquisition-layer SSRF/domain policy.
- Do not place contact email/API credentials into logs beyond approved configuration metadata.

## Change history

### 2026-09-03 — v1

Initial verified Phase 1A contract based on current official Crossref REST, authentication, rate-limit and bulk-data documentation.