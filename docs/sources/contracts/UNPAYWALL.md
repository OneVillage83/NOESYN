# Unpaywall Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Access Resolution
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://unpaywall.org/products/api
  - https://unpaywall.org/data-format
  - https://unpaywall.org/products/snapshot
  - https://unpaywall.org/products/data-feed

## NOESYN role

Unpaywall is a DOI-based **access-resolution source**, not a canonical scholarly identity database and not a rights decision engine.

Use it to discover and rank candidate open-access locations and versions for a DOI.

Do not use it to decide that a requested action such as persistence, redistribution, TTS or commercial derivative creation is permitted.

## Identifiers

- Query identity: normalized DOI.
- Unpaywall's DOI record is an external access observation tied to that DOI.
- Each OA location must remain a separate location/version observation.

## Operations

| Operation | Endpoint/dataset | Supported | Notes |
|---|---|---:|---|
| Resolve DOI | `/v2/{doi}?email=...` | Yes | Primary operation. |
| Search | `/v2/search` | Yes | Secondary discovery only. |
| OA status | DOI object | Yes | Access classification, not reuse authorization. |
| Candidate locations | `oa_locations` | Yes | Preserve all locations. |
| Preferred location | `best_oa_location` | Yes | Provider heuristic; NOESYN may re-rank. |
| Version/license evidence | location fields | Yes | Inputs to Rights Engine. |
| Bulk baseline | OpenAlex snapshot for new integrations | Yes via OpenAlex | Legacy Unpaywall snapshot model has changed. |
| Change feeds | legacy/current paid mechanisms | Conditional | New integrations should prefer OpenAlex-native sync where applicable. |

## Authentication/identification

Current API v2 is free and requires a valid email query parameter. Use a project contact identity from configuration, not a developer's personal hardcoded address.

## Rate limits

Official guidance currently limits API use to **100,000 calls/day**.

NOESYN must:

- cache DOI resolutions with freshness metadata;
- batch/synchronize access observations through appropriate bulk mechanisms at scale;
- retry 429/5xx with jittered backoff;
- avoid one access API call per UI page forever.

## Pagination

Search pagination follows the provider's documented API behavior. The primary production operation is DOI resolution; broad corpus-scale OA synchronization should use bulk/OpenAlex-native mechanisms instead of deep search paging.

## Freshness/update semantics

OA locations can appear, disappear or change version/license/status. Therefore access observations need:

- DOI;
- observed timestamp;
- location URL/host/type;
- version;
- license signal;
- source update metadata when exposed;
- raw/normalized payload hash;
- adapter version.

A newer observation supersedes ranking freshness but does not erase a historical observation used by a prior rights decision or derived artifact.

## Bulk/snapshot strategy

Unpaywall has been integrated operationally with OpenAlex. Current official guidance for new bulk integrations points users toward the latest OpenAlex snapshot rather than assuming the historical standalone semiannual Unpaywall snapshot workflow.

NOESYN strategy:

1. Use Unpaywall API v2 as a focused DOI access resolver during Phase 1.
2. Cache and timestamp responses.
3. At corpus scale, ingest OA/access-location data from the current official OpenAlex snapshot/sync path where equivalent fields are available.
4. Keep adapter provenance so OpenAlex-originated OA observations and direct Unpaywall API observations remain distinguishable.

## Cache/storage policy

Metadata/access observations may be cached under current service rules. Original article content linked from a location is governed by its own rights/license and host terms.

## Rights/license fields

Important fields include:

- `is_oa` / `oa_status`;
- `oa_locations`;
- `best_oa_location`;
- location `license`;
- location `version`;
- host/location characteristics.

Critical rule:

> `is_oa = true` means NOESYN has evidence of a freely accessible version under Unpaywall's classification; it does **not** mean every downstream action is permitted.

A license may be missing, implied or provider-specific. The Rights Engine evaluates the requested action using the exact location/version and other evidence.

## Attribution

Retain Unpaywall provenance for direct API-derived observations. If OA data is instead sourced from OpenAlex snapshot/sync, record `openalex` as the source and the corresponding OpenAlex contract applies.

## Normalized mapping

- `source = unpaywall`
- `source_record_id = normalized DOI`
- observation timestamp
- OA classification observation
- one `AccessLocation` candidate per returned location
- location version/license/host/url
- provider preferred-location flag/score as rebuildable ranking input
- raw payload hash/reference
- adapter version

## Data-quality caveats

- OA classification and URL freshness can change.
- A work may have multiple versions with different licenses.
- `best_oa_location` is provider ranking, not a legal determination.
- license can be null or non-standard.
- DOI-only coverage excludes works without DOIs.

## Failure behavior

- 404/not found: valid negative observation with freshness timestamp, not a permanent truth.
- 429: retry after backoff/quota handling.
- 5xx/network: bounded retry.
- invalid DOI: reject before provider call when normalization/validation proves it invalid.

## Test fixtures

1. gold OA publisher location;
2. repository accepted manuscript;
3. multiple OA locations with different versions/licenses;
4. `is_oa` true but license null;
5. closed DOI;
6. DOI not found;
7. 429/quota response;
8. additive schema field.

## Contract tests

- every location remains distinct;
- provider `best_oa_location` cannot bypass NOESYN rights evaluation;
- `is_oa` alone cannot yield action `ALLOW` for redistribution/TTS/etc.;
- repeated payload ingest is idempotent;
- changed location set creates a new observation state;
- negative lookup can be refreshed later.

## Security notes

- Returned URLs are untrusted and must pass Acquisition SSRF/domain/redirect controls.
- Do not place the configured contact email unnecessarily in application logs.

## Change history

### 2026-09-03 — v1

Initial verified Phase 1A contract. Unpaywall is frozen as a focused access resolver; broad new bulk synchronization should align with OpenAlex rather than a legacy standalone-snapshot assumption.