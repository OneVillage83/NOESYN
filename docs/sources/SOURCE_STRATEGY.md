# NOESYN Scholarly Source Strategy

## Status

- **Phase 1A source-contract evaluation:** COMPLETE
- **Initial universal metadata pair:** Crossref + OpenAlex
- **Authority:** `docs/adr/ADR-0004-initial-sources-and-hybrid-discovery.md`
- **Provider contracts:** `docs/sources/contracts/`

## Goal

NOESYN discovers broadly without treating any single external provider as canonical truth.

The source layer collects **observations**. Canonicalization, version resolution, access selection, rights decisions, artifact identity and evidence semantics happen in NOESYN-owned domains.

## Source classes

### Initial universal metadata/identity sources

#### Crossref — BASELINE

Primary role:

- DOI-centric publisher/member-deposited metadata;
- exact DOI lookup/candidate identity;
- bibliographic metadata;
- funding, ORCID/ROR, licenses, updates/relationships when deposited.

Production contract: `contracts/CROSSREF.md`.

#### OpenAlex — BASELINE

Primary role:

- broad scholarly works/authors/institutions/sources/topics graph;
- citation/reference enrichment;
- non-DOI candidate generation;
- cross-identifiers;
- OA/access-location enrichment;
- official bulk snapshot/incremental synchronization path.

OpenAlex IDs are external aliases, never NOESYN primary keys.

Production contract: `contracts/OPENALEX.md`.

### Biomedical/domain sources

#### PubMed

Role:

- biomedical bibliography;
- PMID identity;
- MeSH/indexing/publication types;
- DOI/PMCID/NCBI cross-links.

Contract: `contracts/PUBMED.md`.

#### PubMed Central (PMC)

Role:

- PMCID/crosswalk observations;
- structured JATS/XML and authorized biomedical full text;
- OA/manuscript dataset observations;
- corrections/version/integrity inputs.

Critical rule: presence in PMC does not equal unrestricted reuse.

Contract: `contracts/PMC.md`.

#### Europe PMC

Role:

- life-science discovery;
- citation/reference/grant/annotation enrichment;
- DOI/PMID/PMCID crosswalks;
- permitted open full-text XML/bulk enrichment.

Contract: `contracts/EUROPE_PMC.md`.

### Access-resolution sources

#### Unpaywall — ENABLED ROLE

Role:

- DOI OA classification/access locations;
- candidate version/license/location observations.

It is an access resolver, not canonical identity or final rights authority. Broad new OA synchronization should align with the current OpenAlex snapshot/sync path rather than legacy assumptions about standalone Unpaywall snapshots.

Contract: `contracts/UNPAYWALL.md`.

#### CORE — LICENSE-GATED

Role when applicable commercial license is approved:

- repository aggregation;
- OA metadata and full-text candidate discovery;
- works without DOI;
- provider/repository provenance.

Current production/commercial use is gated by the terms captured in `contracts/CORE.md`.

### Preprint/version source

#### arXiv

Role:

- version-aware preprint discovery;
- explicit `vN` lineage;
- DOI/publication relationship candidates;
- permitted metadata/content synchronization via official API/OAI/bulk mechanisms.

Article licenses are version-specific; the default arXiv license is not blanket third-party redistribution permission.

Contract: `contracts/ARXIV.md`.

### Strategic enrichment source

#### Semantic Scholar — LICENSE-GATED FOR BROAD PERSISTENT COMMERCIAL DATA USE

Potential roles:

- citation/reference enrichment;
- recommendations;
- semantic discovery;
- graph/source-specific features.

Semantic Scholar is **not** the initial bulk metadata backbone. Broad persistent dataset/commercial production usage remains gated until the applicable current AI2 license is explicitly approved and recorded.

Contract: `contracts/SEMANTIC_SCHOLAR.md`.

### Future/secondary candidates

These remain candidates, not Phase 1A production contracts:

- DOAJ;
- DataCite where relevant to research objects;
- institutional repositories;
- subject repositories;
- publisher APIs/content endpoints where terms permit;
- additional discipline-specific indexes.

Before production use, each needs a dedicated verified source contract.

## Adapter contract

Every adapter documents:

```text
SourceAdapterContract
├─ source name / schema version
├─ supported operations
├─ authentication/identification
├─ rate / concurrency / budget limits
├─ bulk/snapshot options
├─ service/API license gate
├─ content-rights fields and limitations
├─ attribution requirements
├─ caching/storage rules
├─ retry/backoff policy
├─ pagination semantics
├─ update/freshness/reconciliation semantics
├─ identifier namespaces
├─ metadata field mapping
├─ full-text capabilities
├─ known data-quality caveats
├─ terms/policy references
├─ adapter fixtures
└─ date last verified
```

## Standard adapter operations

Not every source implements every operation. Unsupported operations fail explicitly rather than returning misleading empty results.

Candidate internal interfaces:

- `search_works(query, filters, cursor)`
- `get_work_by_external_id(namespace, value)`
- `get_author(...)`
- `get_citations(...)`
- `get_references(...)`
- `get_updates_since(...)`
- `resolve_access_locations(...)`
- `retrieve_artifact(...)`
- `get_rights_metadata(...)`

The exact code interface may evolve in Phase 1B, but source capability/unsupported-state semantics may not be hidden.

## Provider-neutral source observation

A source adapter emits a provider-neutral observation rather than mutating canonical entities directly.

Minimum durable observation concepts:

```text
SourceRecordObservation
├─ noesyn UUIDv7 observation ID
├─ source/provider
├─ provider record ID
├─ observed_at
├─ provider update timestamp(s)
├─ adapter/schema version
├─ raw payload hash/reference (where retention permits)
├─ normalized payload hash
├─ external identifiers
├─ candidate metadata
├─ relationship observations
├─ access/license observations
└─ source-specific quality/status metadata
```

Canonicalization is a separate Identity operation with its own decision provenance.

## Federated/local search strategy

ADR-0004 freezes a **hybrid local-first** strategy.

### Stage 1 — query understanding

Normalize:

- free-text terms;
- exact title/author/DOI/PMID/arXiv requests;
- date ranges;
- fields/disciplines;
- publication types;
- access preferences;
- citation/author constraints.

### Stage 2 — local candidate generation

Search locally synchronized/canonical metadata first using the initial PostgreSQL search projection.

### Stage 3 — permitted external expansion

Query applicable source APIs for candidates/freshness when local coverage is insufficient or source-specific search is useful.

Respect:

- license gates;
- current quotas/budgets;
- source-specific rate policies;
- query/page limits;
- provider availability.

### Stage 4 — provider-neutral normalization

Map every source response into source observations.

### Stage 5 — identity resolution

Resolve candidates into NOESYN `ScholarlyWork` / `WorkVersion` identities using exact identifiers plus controlled confidence-based matching.

### Stage 6 — ranking

Ranking may combine:

- lexical/semantic relevance;
- field/date intent;
- source agreement;
- document/version type;
- integrity flags;
- access state;
- user context.

Citation count is a feature, never a truth/evidence-quality score.

## Deduplication signals

### High confidence

- normalized DOI match;
- trusted PMID/PMCID crosswalk;
- exact arXiv base/version relationship;
- trusted provider cross-identifiers.

### Secondary/confidence-scored

- normalized title similarity;
- author overlap/order;
- publication-date proximity;
- venue/source;
- abstract similarity;
- reference fingerprints;
- document hashes.

Ambiguous matches remain unresolved rather than being forced.

## Freshness and replay

Each source observation records:

- source;
- provider record ID;
- observation timestamp;
- source update timestamp where available;
- adapter version;
- payload/normalized hash;
- sync/reconciliation checkpoint context when relevant.

Incremental synchronization does not destroy prior observations used by historical outputs.

OpenAlex merges/deletions and similar provider topology changes require reconciliation jobs; upstream provider IDs disappearing must not mutate NOESYN canonical IDs.

## Source-quality policy

Track quality dimensions such as:

- identifier completeness;
- title/author disagreement;
- abstract availability;
- citation disagreement;
- access-location freshness;
- license evidence quality;
- version clarity;
- retraction/update timeliness.

Provider disagreement is surfaced to Identity/Policy systems rather than silently resolved by one permanent source-precedence list.

## Scaling strategy

Start API-first for controlled adapter correctness and fixtures, but **do not design one request per paper forever**.

At scale:

- Crossref → official data files/snapshots;
- OpenAlex → official snapshot + incremental/reconciliation;
- PubMed/PMC/Europe PMC → official bulk/history/cloud/OAI mechanisms appropriate to content rights;
- arXiv → OAI/bulk mechanisms;
- Unpaywall OA baseline → current OpenAlex-native bulk path where applicable;
- CORE/Semantic Scholar → only under approved provider licenses/agreements.

Bulk-derived and API-derived observations must normalize into compatible provider-neutral observations while retaining their acquisition provenance.

## Rights separation rule

Three different questions must never be conflated:

1. **May NOESYN call/use the provider service or dataset?**
2. **May NOESYN retrieve/store/process the underlying article/version?**
3. **May NOESYN perform the requested derivative action (display/TDM/summary/TTS/redistribution/commercial use)?**

A provider API license can permit #1 without granting #2/#3. An article Creative Commons license can permit some #2/#3 actions while provider API terms still control #1.

## Phase 1B source implementation order

1. Crossref adapter + fixtures/contract tests;
2. OpenAlex adapter + fixtures/contract tests;
3. provider-neutral observation persistence;
4. DOI/external identifier normalization;
5. deterministic identity resolution;
6. hybrid local search/candidate expansion;
7. Temporal durable sync/reconciliation;
8. only then add further provider adapters according to roadmap need and license state.

## Drift rule

Before modifying a production adapter based on observed new behavior:

1. reverify current official documentation;
2. update the provider contract and verification date;
3. update fixtures/contract tests;
4. record the engineering-log change;
5. determine whether historical observations/derived artifacts require reprocessing or reinterpretation.
