# NOESYN Source Contracts

Every production scholarly-source adapter must have a contract in this directory. Contracts are snapshots of verified provider behavior and must be updated when the provider changes.

## Phase 1A contract status

| Provider | Contract | Status | Planned role |
|---|---|---|---|
| Crossref | `CROSSREF.md` | VERIFIED | DOI/publisher-deposited metadata backbone |
| OpenAlex | `OPENALEX.md` | VERIFIED | broad graph/metadata/citation/access enrichment and bulk baseline |
| Semantic Scholar | `SEMANTIC_SCHOLAR.md` | VERIFIED WITH COMMERCIAL-USE GATE | citation/recommendation/semantic enrichment after applicable license clearance |
| Unpaywall | `UNPAYWALL.md` | VERIFIED | DOI access/OA-location resolver |
| PubMed | `PUBMED.md` | VERIFIED | biomedical bibliography, PMID, MeSH/indexing |
| PubMed Central | `PMC.md` | VERIFIED | structured JATS/full text under exact article/collection rights |
| Europe PMC | `EUROPE_PMC.md` | VERIFIED | life-science discovery, graph and permitted OA/full-text enrichment |
| CORE | `CORE.md` | VERIFIED WITH COMMERCIAL-LICENSE GATE | repository/OA/full-text discovery after applicable commercial license |
| arXiv | `ARXIV.md` | VERIFIED | version-aware preprint discovery and permitted content acquisition |

### Phase 1A source decision

The initial universal metadata pair is **Crossref + OpenAlex** under `ADR-0004`.

Source contracts being `VERIFIED` means the documented provider behavior was checked against current official provider material on the verification date. It does **not** mean every source is enabled for every environment/action. Source-specific license gates and the NOESYN Rights Engine still apply.

## Contract template

```markdown
# <Provider> Source Contract

- Status: DRAFT | VERIFIED | VERIFIED WITH ... GATE | DEPRECATED
- Last verified: YYYY-MM-DD
- Adapter owner:
- Adapter/schema version:
- Official documentation URLs:
- Terms/policy URLs:

## NOESYN role

What this source is authoritative/useful for and what it is explicitly **not** treated as authoritative for.

## Identifiers

- provider record ID:
- DOI support:
- PMID/PMCID/arXiv/etc:
- normalization rules:
- known alias/version semantics:

## Operations

| Operation | Endpoint/dataset | Supported | Notes |
|---|---|---:|---|
| Search works | | | |
| Fetch by ID | | | |
| Authors | | | |
| Citations | | | |
| References | | | |
| Access locations | | | |
| Full text | | | |
| Incremental updates | | | |
| Bulk snapshot | | | |

## Authentication

Exact current authentication/identification rules.

## Rate/concurrency limits

Document official limits, polite-use requirements, backoff expectations, and headers/statuses.

## Pagination

Cursor/page semantics and known limits.

## Freshness/update semantics

How to detect changes and what source timestamps mean.

## Bulk/snapshot strategy

Available official bulk mechanism, update/diff mechanism, cost/storage considerations.

## Cache/storage policy

What NOESYN may cache/store and for how long under current provider terms.

## Rights/license fields

List exact fields and what they do **not** prove. Separate provider service terms from content license evidence.

## Attribution

Required product/publication attribution.

## Normalized mapping

Map provider fields to `SourceRecordObservation` and canonical candidate fields.

## Data-quality caveats

Known incompleteness, ambiguity, delays, duplicates, version issues, missing fields.

## Failure behavior

Expected status codes, rate-limit behavior, retryable/non-retryable errors, outages.

## Test fixtures

List permanent fixtures and the behavior each proves.

## Contract tests

Automated tests that detect schema/behavior drift where feasible.

## Security notes

Credentials, URL/retrieval risks, untrusted payload details.

## Change history

Timestamped provider-contract changes.
```

## Verification rule

A source contract is not `VERIFIED` because a blog post or third-party library says how an API works. Use current official provider documentation and, where useful, controlled live requests.

## License-gate rule

If provider terms require an account-specific/commercial license for NOESYN's intended production use, the contract must say so and production adapter workflows must fail closed until that gate is explicitly approved.

A service license does not automatically grant article/full-text copyright permissions, and an article license does not automatically grant provider API/dataset usage rights.

## Drift rule

When adapter behavior differs from the verified contract:

1. stop assuming the old behavior is correct;
2. determine whether the provider changed or the adapter is wrong;
3. update the contract and tests;
4. record the change in the engineering log;
5. evaluate whether historical source observations/derived artifacts are affected.
