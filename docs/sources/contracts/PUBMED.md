# PubMed / NCBI Bibliographic Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://www.ncbi.nlm.nih.gov/books/NBK25501/
  - https://www.ncbi.nlm.nih.gov/books/NBK25497/
  - https://pubmed.ncbi.nlm.nih.gov/help/
- **E-utilities base:** `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/`

## NOESYN role

PubMed is NOESYN's biomedical bibliographic/domain-enrichment source.

Use it for:

- PMID identity observations;
- biomedical title/author/journal/publication metadata;
- publication types and MeSH/biomedical indexing where exposed;
- links/crosswalks into PMC and other NCBI resources;
- biomedical discovery and source-specific query behavior.

PubMed is **not** a general full-text repository and presence in PubMed does not imply open access or reuse rights.

## Identifiers

- Primary provider identifier: PMID.
- Preserve DOI, PMCID and other linked identifiers when provided.
- PMID remains an external alias, never the NOESYN canonical primary key.
- PMID-to-PMCID/DOI crosswalk observations must retain source/timestamp provenance.

## Operations

| Operation | NCBI E-utility | Supported | Notes |
|---|---|---:|---|
| Search PubMed | `ESearch` | Yes | Use PubMed database and NCBI query syntax. |
| Fetch records | `EFetch` | Yes | Structured bibliographic retrieval. |
| Summaries | `ESummary` | Yes | Lightweight metadata. |
| Link/crosswalk | `ELink` | Yes | Related NCBI resources/PMCID paths. |
| Batch/history | `EPost` + History server | Yes | Preferred for larger request sets. |
| Full text | No | No | Resolve PMC/publisher/repository separately. |
| Incremental discovery | date/update query patterns | Yes | Query semantics must be recorded in checkpoints. |

## Identification/authentication

NCBI asks programmatic clients to identify themselves using `tool` and `email`. For production applications, NOESYN should register/use a stable project identity and API key where needed.

Configuration values must not be hardcoded into source files.

## Rate and usage policy

Current NCBI E-utilities guidance:

- without API key: no more than 3 requests/second;
- with API key: default allowance up to 10 requests/second;
- higher rates require coordination/approval;
- large jobs should be scheduled during lower-traffic periods (weekends or roughly 9 PM–5 AM Eastern on weekdays) and use History/batching rather than many individual calls.

The adapter must enforce a source-specific limiter and react to NCBI runtime responses.

## Pagination/batching

- Use ESearch + History server / WebEnv/query-key workflows for large result sets where appropriate.
- Batch EFetch rather than one PMID per request.
- Persist the exact query, date/update filters, history/checkpoint metadata and adapter version.

## Freshness/update semantics

PubMed records can be corrected, indexed further and linked after initial publication. NOESYN must preserve source observations rather than destructively overwriting historical source state.

Record:

- observed timestamp;
- PMID;
- source modification/indexing dates when available;
- adapter version;
- payload hash/reference;
- query/checkpoint context for incremental jobs.

## Bulk strategy

Phase 1 uses E-utilities for controlled correctness and biomedical enrichment. If NOESYN later requires a large local PubMed baseline, evaluate the official NLM baseline/update-file mechanisms and their then-current terms separately before implementation; do not scale by issuing one EFetch per PMID indefinitely.

## Cache/storage policy

Bibliographic metadata may be stored as source observations under NCBI/NLM terms, but abstracts and other supplied content can have independent copyright. NCBI's copyright/disclaimer requirements must remain visible in product/legal design.

## Rights/license fields

PubMed bibliographic inclusion, PMID and abstract availability do **not** prove full-text access/reuse permission. Any abstract/full-text/PMC link enters the Access Resolver/Rights Engine separately.

## Attribution

Retain NCBI/PubMed provenance and comply with current NCBI disclaimer/copyright/identification requirements in any public use of NCBI-derived content.

## Normalized mapping

- `source = pubmed`
- `source_record_id = PMID`
- observation/source timestamps
- raw payload hash/reference
- adapter version
- title/authorship/venue/date/type candidates
- DOI/PMCID/external-ID observations
- MeSH/indexing observations
- related-resource link observations

## Data-quality caveats

- PubMed is biomedical/life-science focused, not universal scholarly coverage.
- Indexing/MeSH can lag publication.
- Abstract availability varies and may be copyrighted.
- Date fields represent multiple concepts and must remain typed.
- links to PMC/publisher records do not establish reuse rights.

## Failure behavior

- NCBI rate-limit response: retry after provider-safe delay; reduce concurrency.
- malformed query/invalid parameter: non-retryable until corrected.
- transient 5xx/network: bounded retry with jitter.
- partial/batch response: detect missing requested PMIDs and record/retry explicitly.

## Test fixtures

1. DOI-linked PMID;
2. PMID with PMCID;
3. rich MeSH/publication-type record;
4. sparse/no-abstract record;
5. corrected/updated record;
6. batched EFetch response;
7. rate-limit response;
8. missing PMID in batch.

## Contract tests

- PMID remains external alias.
- repeated ingest is idempotent.
- updated source payload creates a new observation.
- abstract presence cannot authorize full-text actions.
- batch completeness is checked.
- History/checkpoint replay is deterministic.

## Security notes

- XML/text payloads are untrusted and parsed with safe XML settings.
- provider URLs/links do not bypass Acquisition policy.
- NCBI key and contact configuration are secrets/configuration, not source code.

## Change history

### 2026-09-03 — v1

Initial verified PubMed/NCBI bibliographic contract for Phase 1A.