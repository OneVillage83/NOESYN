# PubMed Central (PMC) Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Document Acquisition / Source Integration
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://pmc.ncbi.nlm.nih.gov/tools/developers/
  - https://pmc.ncbi.nlm.nih.gov/tools/oai/
  - https://pmc.ncbi.nlm.nih.gov/tools/cloud/
  - https://pmc.ncbi.nlm.nih.gov/tools/id-converter-api/
  - https://pmc.ncbi.nlm.nih.gov/tools/oa-service/
- **Current OAI-PMH base:** `https://pmc.ncbi.nlm.nih.gov/api/oai/v1/mh/`

## NOESYN role

PMC is a premier biomedical/life-science structured-document source and an important PMCID/version/access authority.

Use it for:

- PMCID identity/crosswalk observations;
- lawful structured JATS/XML acquisition where the applicable collection/license permits;
- Open Access Subset and other explicitly permitted machine-access workflows;
- high-fidelity document structure for the shared NOESYN Document Platform;
- corrections/version/integrity observations where available.

Critical rule:

> **Presence in PMC does not mean unrestricted reuse.**

Every acquisition/storage/display/TDM/TTS/redistribution action must be evaluated against the exact article/version/collection/license.

## Identifiers

- PMCID is the provider article identifier.
- Preserve PMID, DOI and manuscript IDs/crosswalks where available.
- PMCID is an external alias, not a NOESYN primary key.

## Approved programmatic mechanisms

PMC explicitly directs automated retrieval through approved developer services such as:

- PMC Cloud Service / datasets;
- PMC OAI-PMH;
- PMC FTP/dataset mechanisms where currently documented;
- NCBI E-utilities;
- BioC;
- OA Web Service;
- ID Converter.

NOESYN must not scrape article web pages as its document-ingestion mechanism.

## Operations

| Operation | Mechanism | Supported | Notes |
|---|---|---:|---|
| PMCID/PMID/DOI crosswalk | ID Converter | Yes | Identity observation. |
| Metadata/full-text OAI | OAI-PMH | Yes | Full text only where usage rights permit. |
| OA package resolution | OA Web Service | Yes | Returns permitted OA package/link info. |
| Structured JATS/XML | OAI/Cloud/OA datasets | Yes | Preferred document input. |
| BioC representation | BioC API | Yes | Optional NLP/document interchange. |
| Bulk OA corpus | PMC Cloud / OA datasets | Yes | Preferred for large-scale permitted ingest. |
| Non-OA bulk full text | No general permission | No | Do not automate unauthorized download. |

## OAI-PMH semantics

Current PMC OAI-PMH endpoint uses OAI-PMH 2.0.

Relevant current behaviors include:

- `set=pmc-open` for the reusable/open collection;
- metadata formats including front matter and full PMC/JATS representations where permitted;
- resumption-token pagination;
- provider-side record limits per response.

NOESYN should favor exact machine-readable source anchors from JATS rather than PDF text extraction whenever allowed.

## Rate/concurrency limits

Current PMC OAI guidance documents a high-volume guideline of approximately:

- maximum 3 requests/second;
- no concurrent OAI requests;
- large runs should avoid NCBI peak hours, especially when making more than roughly 100 requests.

Runtime/provider responses remain authoritative. Bulk corpus acquisition should use PMC's official dataset/cloud mechanism rather than hammering OAI record-by-record.

## Freshness/update semantics

PMC records can be corrected, updated, retracted, versioned or have rights metadata change. Preserve:

- PMCID;
- source/version/update timestamps;
- article/collection/license observations;
- retrieval timestamp;
- exact artifact hash;
- adapter/parser version;
- machine-access mechanism used.

Never replace a previously processed artifact without retaining the dependency link from derived outputs to the old artifact/document version.

## Bulk/snapshot strategy

For permitted large-scale full-text processing, prefer the current official PMC Cloud/dataset distribution architecture. The Open Access Subset contains articles available under Creative Commons or other explicit publisher permissions, but licenses still vary article by article.

Author manuscript datasets have their own terms and must remain separate rights classes.

## Cache/storage policy

Retention is article/action/license specific.

Possible NOESYN classes:

- reusable/public OA artifact;
- permitted TDM but limited redistribution;
- author-manuscript-specific terms;
- metadata-only;
- transient/user-entitled where a future workflow permits.

The Rights Engine, not the PMC adapter, decides which class applies.

## Rights/license evidence

Preserve:

- article license URI/text where exposed;
- PMC collection/dataset membership;
- version/manuscript type;
- publisher permissions when encoded;
- retrieval mechanism.

`pmc-open` membership is useful evidence of machine-readable reusable availability, but exact license terms still govern individual downstream actions.

## Attribution

Retain PMC/NCBI provenance and any article-license attribution requirements. Original article attribution must remain associated with displayed/derived material when required.

## Normalized mapping

- `source = pmc`
- PMCID source record ID
- PMID/DOI external identifiers
- observation/update timestamps
- article/collection/license evidence
- artifact locator and acquisition mechanism
- raw artifact hash
- JATS metadata/version information
- integrity/update links

Structured parsing occurs in the shared Document Platform, not inside the source adapter.

## Data-quality caveats

- Not all PMC records are reusable full text.
- Licenses vary even inside reusable/open collections.
- Author manuscripts and versions of record can differ.
- PMCID/PMID/DOI relationships are identity evidence, not proof two artifacts are byte/content identical.
- Figures/supplementary files can have separate restrictions/availability.

## Failure behavior

- OAI resumption token expired/invalid: restart from durable synchronization boundary.
- rate limit/temporary service error: bounded retry with provider-safe backoff.
- rights/collection mismatch: `CONTENT_ACTION_NOT_AUTHORIZED`, not generic fetch failure.
- malformed XML/package: quarantine artifact and preserve hash for parser investigation.
- missing crosswalk: retain unresolved identifier observation.

## Test fixtures

1. CC BY OA JATS article;
2. OA article with a different Creative Commons license;
3. author manuscript case;
4. PMC metadata record without permitted reusable full text;
5. PMID/PMCID/DOI crosswalk;
6. correction/retraction/version link;
7. OAI resumption-token page;
8. malformed/edge-case JATS;
9. figure/table/math-rich article.

Permanent full-text fixtures must themselves have clear storage/test rights.

## Contract tests

- PMCID remains an external alias.
- no PMC-presence flag automatically authorizes display/TTS/redistribution.
- JATS artifact hash/version is retained.
- derived document points to exact artifact.
- license/collection evidence is persisted separately from final RightsDecision.
- OAI synchronization is replayable/idempotent.

## Security notes

- Parse XML with XXE/external-entity protections.
- Treat archives/supplementary files as untrusted uploads.
- Enforce archive expansion/resource limits.
- Use only approved PMC endpoints; no arbitrary page scraping.

## Change history

### 2026-09-03 — v1

Initial verified Phase 1A PMC contract, with action-specific licensing and structured JATS acquisition frozen as architectural requirements.