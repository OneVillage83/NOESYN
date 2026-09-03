# Europe PMC Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration / Document Acquisition
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://europepmc.org/developers
  - https://europepmc.org/RestfulWebService
  - https://europepmc.org/downloads
- **REST base:** `https://www.ebi.ac.uk/europepmc/webservices/rest`

## NOESYN role

Europe PMC is a major life-sciences discovery, relationship and open/full-text enrichment source complementary to PubMed/PMC.

Use it for:

- life-science bibliographic discovery;
- PMID/PMCID/DOI and Europe-PMC-source identifier crosswalks;
- references/citations and related-information observations;
- grants/annotations where useful;
- open full-text XML and supplementary resources where permitted;
- bulk OA and metadata synchronization.

It is not a general authorization to bulk-download non-open content.

## Identifiers

Europe PMC records carry a `source` namespace plus source ID. Preserve the namespace; a numeric ID without its source is ambiguous.

Cross-identifiers such as PMID, PMCID and DOI remain separate external aliases.

## Operations

| Operation | Endpoint/dataset | Supported | Notes |
|---|---|---:|---|
| Search | `/search` | Yes | XML/JSON output. |
| Article record | `/article/{source}/{id}` | Yes | Source namespace required. |
| References | `/{source}/{id}/references` | Yes | Graph observation. |
| Citations | citation endpoints | Yes | Graph observation. |
| Open full-text XML | `/{id}/fullTextXML` where eligible | Yes | OA/full-text subset only. |
| Supplementary files | documented OA mechanisms | Conditional | Availability/rights vary. |
| Status updates | update/status endpoints | Yes | Integrity/version enrichment. |
| OAI | Europe PMC OAI service | Yes | Metadata/OA use. |
| Bulk datasets | official downloads/FTP | Yes | Preferred at scale. |

## Authentication/rate behavior

The public developer APIs do not currently advertise an API-key requirement for ordinary REST access. During Phase 1A verification, a fixed universal requests-per-second quota was not found in the principal official REST documentation.

Therefore NOESYN must use a conservative configurable source limiter, identify itself where supported, obey runtime throttling/error responses and reverify any published quota immediately before production rollout. Absence of a published fixed RPS is **not** permission for unbounded concurrency.

## Pagination

Use the provider's cursor/page mechanisms documented for REST search and resumption tokens for OAI/bulk-style traversal. Persist query, cursor/token, result-type/schema version and checkpoint state.

## Freshness/update semantics

Europe PMC records can change through indexing, manuscript/full-text availability and status updates. Preserve source observation timestamps, provider update/status metadata, payload hashes and adapter version.

Integrity/update relationships must be represented as explicit observations rather than destructive metadata replacement.

## Bulk strategy

Europe PMC provides official downloadable datasets including open-access/full-text collections, metadata sets and identifier mappings. At scale:

1. API for interactive/exact discovery and development;
2. official bulk datasets for broad corpus synchronization;
3. incremental/status workflows for freshness where documented.

Do not automate bulk downloading of non-OA content; Europe PMC explicitly distinguishes OA/full-text mechanisms from non-open records.

## Cache/storage policy

Bibliographic and OA dataset use follows the provider/source and underlying article licenses. Full text in the OA subset remains copyrighted and can have differing article licenses.

Original content rights must be evaluated per article/location/action.

## Rights/license evidence

Preserve:

- OA/full-text availability flags;
- article license where exposed;
- exact source/version/location;
- dataset/collection membership.

No Europe-PMC OA flag automatically grants every NOESYN action. The Rights Engine evaluates display, TDM, embedding, summary, TTS, persistence and redistribution separately.

## Attribution

Retain Europe PMC/EMBL-EBI provenance and article-specific attribution/license requirements. Recheck provider attribution text before public API/UI launch.

## Normalized mapping

- `source = europe_pmc`
- provider source namespace + record ID
- observation/update timestamp
- raw payload hash/reference
- adapter version
- DOI/PMID/PMCID aliases
- title/authorship/venue/type candidates
- references/citations
- grants/annotations/status observations
- OA/access/license observations
- eligible full-text artifact locator

## Data-quality caveats

- Coverage is life-sciences oriented, not universal.
- Citation/reference counts and links can differ from other graphs.
- OA/full-text availability can lag or change.
- Multiple source namespaces require careful ID handling.
- OA articles can have different licenses.
- Supplementary-resource rights/availability may differ from main text.

## Failure behavior

- 429/throttle: retry with increasing backoff and reduce concurrency.
- 5xx/network: bounded jittered retries.
- invalid source/ID/query: non-retryable until corrected.
- full-text unavailable because record is not in eligible subset: explicit `CONTENT_NOT_AVAILABLE_UNDER_SOURCE_CONTRACT`.
- malformed XML: quarantine and preserve artifact/hash.

## Test fixtures

1. PMID/PMCID/DOI-rich record;
2. open JATS/XML article;
3. metadata-only/non-OA record;
4. citations/references;
5. grants/annotations case;
6. status/correction case;
7. pagination/cursor response;
8. differing article licenses;
9. sparse record.

## Contract tests

- source namespace is part of external identity.
- OA/fulltext flags do not bypass Rights Engine.
- repeated ingestion is idempotent.
- source updates create new observations.
- citation/reference edges retain provenance.
- bulk/API observations normalize into the same provider-neutral shape.

## Security notes

- XML is untrusted; disable dangerous external entities.
- URLs/supplementary resources pass Acquisition SSRF/content-size policy.
- downloaded archives/files are resource-limited and malware/untrusted-content treated.

## Change history

### 2026-09-03 — v1

Initial verified Phase 1A Europe PMC contract. A conservative configurable throttle is required because no single fixed public RPS quota was found in the principal current official REST documentation reviewed.