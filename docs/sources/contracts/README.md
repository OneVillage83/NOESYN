# NOESYN Source Contracts

Every production scholarly-source adapter must have a contract in this directory. Contracts are snapshots of verified provider behavior and must be updated when the provider changes.

## Required filename

Use a stable provider name, for example:

- `CROSSREF.md`
- `OPENALEX.md`
- `SEMANTIC_SCHOLAR.md`
- `UNPAYWALL.md`
- `PMC.md`
- `EUROPE_PMC.md`
- `CORE.md`
- `ARXIV.md`

## Contract template

```markdown
# <Provider> Source Contract

- Status: DRAFT | VERIFIED | DEPRECATED
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

## Drift rule

When adapter behavior differs from the verified contract:

1. stop assuming the old behavior is correct;
2. determine whether the provider changed or the adapter is wrong;
3. update the contract and tests;
4. record the change in the engineering log;
5. evaluate whether historical source observations/derived artifacts are affected.
