# NOESYN Phase 1A Status

- **Phase:** 1A — Source Contracts & Stack Evaluation
- **Status:** COMPLETE — IMPLEMENTATION BASELINE FROZEN
- **Date:** 2026-09-03
- **Production code status:** Not yet scaffolded
- **Next phase:** 1B — Core Identity Persistence & First Metadata Vertical Slice

## What Phase 1A accomplished

Phase 1A converted Phase 0's intentionally open implementation choices into verified provider contracts and ADR-backed technology decisions without writing throwaway application code.

### Verified source contracts

- Crossref — VERIFIED;
- OpenAlex — VERIFIED;
- Semantic Scholar — VERIFIED WITH COMMERCIAL-USE GATE;
- Unpaywall — VERIFIED;
- PubMed — VERIFIED;
- PubMed Central — VERIFIED;
- Europe PMC — VERIFIED;
- CORE — VERIFIED WITH COMMERCIAL-LICENSE GATE;
- arXiv — VERIFIED.

Provider contracts live under `docs/sources/contracts/` and record current official endpoint/auth/rate/bulk/rights/attribution/failure/fixture requirements.

## Source architecture decisions frozen

### Initial universal metadata pair

**Crossref + OpenAlex**.

Rationale:

- Crossref provides DOI-centric publisher/member-deposited metadata and an official large-scale data-file path;
- OpenAlex provides broad graph/candidate/citation/access enrichment, CC0 metadata under current terms, and an official snapshot/incremental path;
- neither source becomes canonical identity;
- provider records remain append-oriented `SourceRecordObservation` inputs.

### Supporting source roles

- PubMed — biomedical bibliography, PMID, MeSH/indexing;
- PMC — PMCID and structured JATS/full text only under exact article/collection rights;
- Europe PMC — complementary life-science graph/full-text enrichment;
- Unpaywall — focused DOI access/OA-location resolver;
- arXiv — exact version-aware preprint source;
- Semantic Scholar — strategic citation/recommendation enrichment behind applicable commercial/persistent-data license gate;
- CORE — strategic repository/full-text discovery behind applicable commercial-product license gate.

### Discovery strategy

Hybrid local-first:

1. search synchronized/canonical local metadata first;
2. use permitted provider APIs for expansion/freshness;
3. normalize/canonicalize/deduplicate before durable insertion;
4. migrate broad synchronization toward official provider snapshots/bulk mechanisms;
5. do not depend permanently on one external API request per user search.

## Implementation stack decisions frozen

### Backend/workers

- Python 3.14 line;
- FastAPI;
- Pydantic boundaries;
- SQLAlchemy 2.x + Alembic;
- `uv` dependency/environment locking.

### Web

- TypeScript;
- React via Next.js App Router;
- `pnpm` dependency locking;
- generated TypeScript API client/types from FastAPI OpenAPI.

### Canonical persistence

- PostgreSQL 18+;
- UUIDv7 durable NOESYN entity IDs;
- append-oriented relational source observations/dependency lineage plus materialized current canonical state;
- not full event sourcing.

### Search/graph/vector

- PostgreSQL native metadata full-text search initially;
- typed graph edges in relational tables initially;
- dedicated search engine deferred until measured need;
- vector technology deferred until evaluated AI retrieval workloads;
- specialized graph database deferred until measured traversal workloads.

### Durable background orchestration

- Temporal for multi-step durable workflows;
- Activities own side effects and must be idempotent;
- important Workflow definition changes require replay testing.

### Object storage

- S3-compatible private object-storage boundary;
- production vendor intentionally deferred;
- Rights Engine controls retention/serving; storage technology never grants permission.

## Accepted ADRs added in Phase 1A

- `ADR-0002` — mixed Python/TypeScript application stack;
- `ADR-0003` — PostgreSQL canonical core, UUIDv7, append-oriented provenance;
- `ADR-0004` — Crossref + OpenAlex initial sources and hybrid discovery;
- `ADR-0005` — Temporal durable workflow orchestration;
- `ADR-0006` — S3-compatible artifact storage boundary.

## Decisions intentionally still deferred/open

Tracked in `docs/architecture/OPEN_DECISIONS.md`, including:

- production hosting/cloud;
- production S3-compatible storage vendor;
- AI provider/model;
- TTS provider/model;
- vector engine;
- specialized graph/search engines beyond initial PostgreSQL projections;
- native mobile strategy;
- authentication provider;
- formal accessibility target;
- observability vendor;
- commercial/legal operating policy;
- permanent evaluation-corpus licensing.

These do not block Phase 1B.

## Legal/license gates that remain fail-closed

### Semantic Scholar

Broad persistent/commercial dataset use is not automatically enabled. Applicable AI2 licensing must be explicitly approved/recorded before that workflow becomes production-active.

### CORE

NOESYN's planned public/commercial search/discovery product falls within categories that current CORE terms require to contact/license. Production CORE integration therefore remains gated until the applicable license is obtained/recorded.

### Full text generally

Provider/API service permission and underlying article-content rights are separate. No source `is_oa`/PMC-presence/full-text-link flag bypasses the Rights Engine.

## Phase 1A checks

- verified provider behavior against current official documentation rather than third-party libraries alone;
- separated API/service licensing from article-content licensing;
- reconciled `OPEN_DECISIONS.md` with accepted ADRs;
- updated source strategy to remove pre-contract assumptions;
- updated source-contract index;
- updated `README.md`, `CODEX_START_HERE.md`, and `ROADMAP.md` so no stale “stack unfrozen” instruction remains in primary handoff docs;
- no runtime/unit test suite was applicable because application code has not yet been scaffolded.

## Phase 1B starting boundary

Phase 1B may now initialize implementation code, but only within the frozen architecture.

Recommended implementation order:

1. scaffold `apps/api`, `apps/web`, Python domain/source packages, Temporal worker and local infrastructure;
2. initialize `uv`, `pnpm`, Python/Node version files and committed lockfiles;
3. initialize PostgreSQL/Alembic schema baseline;
4. implement UUIDv7 ID conventions;
5. implement `SourceRecordObservation`, `ScholarlyWork`, `WorkVersion`, `ExternalIdentifier`, and identity-decision provenance;
6. implement Crossref adapter from the verified contract and deterministic fixtures;
7. implement OpenAlex adapter from the verified contract and deterministic fixtures;
8. implement DOI normalization/exact matching first, then controlled confidence matching;
9. implement Temporal synchronization/reconciliation including OpenAlex merge/deletion handling;
10. implement PostgreSQL metadata search and baseline FastAPI endpoints;
11. generate the TypeScript client from OpenAPI and add schema-drift CI;
12. prove migration correctness, idempotency, replay and provenance before provider expansion.

## Exit statement

Phase 1A is complete. The project is now ready to begin production scaffolding and canonical identity implementation without reopening the source/stack decisions that were intentionally deferred during Phase 0.