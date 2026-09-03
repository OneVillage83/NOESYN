# NOESYN Roadmap

## Roadmap doctrine

Implementation phases are vertical slices of the target architecture. Phase numbers describe build order, not permanent product boundaries.

Every phase must update documentation and the timestamped engineering log.

## Phase 0 — Architecture & Governance

**Status:** COMPLETE — architecture freeze candidate

**Goal:** define the system before implementation choices harden the wrong assumptions.

Deliverables:

- [x] project mission/constitution;
- [x] target architecture;
- [x] core canonical data model;
- [x] rights/access model;
- [x] scholarly source strategy;
- [x] shared document platform architecture;
- [x] evidence-grounded AI architecture;
- [x] audio architecture;
- [x] knowledge graph architecture;
- [x] reader architecture;
- [x] security/privacy baseline;
- [x] architecture-first ADR;
- [x] public-repo secret/artifact ignore baseline;
- [x] implementation roadmap;
- [x] Codex/contributor start-here guide;
- [x] changelog and timestamped engineering-log convention;
- [x] freeze core implementation stack after Phase 1A source-contract/technology evaluation;
- [ ] qualified legal review before workflows involving commercial-scale restricted full-text processing or redistribution.

Phase 0 architecture status: `docs/status/PHASE_0_STATUS.md`.

---

## Phase 1 — Source Contracts & Core Identity

**Goal:** prove canonical identity and source adapters with metadata before full-text complexity.

### Phase 1A — Source Contracts & Stack Evaluation

**Status:** COMPLETE

Deliverables:

- [x] verified Crossref contract;
- [x] verified OpenAlex contract;
- [x] verified Semantic Scholar contract with commercial-use gate;
- [x] verified Unpaywall contract;
- [x] verified PubMed contract;
- [x] verified PMC contract;
- [x] verified Europe PMC contract;
- [x] verified CORE contract with commercial-license gate;
- [x] verified arXiv contract;
- [x] select initial universal metadata pair: Crossref + OpenAlex;
- [x] select backend/web language/framework boundary;
- [x] select canonical relational database;
- [x] select internal ID format;
- [x] select provenance persistence pattern;
- [x] select initial search projection strategy;
- [x] select durable workflow orchestration;
- [x] select object-storage interface boundary;
- [x] record decisions in ADRs and reconcile `OPEN_DECISIONS.md`;
- [x] update `README.md` and `CODEX_START_HERE.md` handoff.

Authority:

- `docs/architecture/PHASE_1A_STACK_EVALUATION.md`
- `docs/sources/contracts/README.md`
- ADR-0002 through ADR-0006.

### Phase 1B — Core Identity Persistence & First Metadata Vertical Slice

**Status:** NEXT

Deliverables:

- [ ] initialize mixed Python/TypeScript repository structure;
- [ ] initialize `uv`/Python and `pnpm`/TypeScript lockfiles;
- [ ] local PostgreSQL 18+ development environment;
- [ ] FastAPI application baseline and generated OpenAPI contract;
- [ ] Next.js application baseline and generated TypeScript API client;
- [ ] SQLAlchemy/Alembic baseline;
- [ ] UUIDv7 internal ID convention;
- [ ] provider-neutral `SourceRecordObservation` schema;
- [ ] canonical `ScholarlyWork` schema;
- [ ] canonical `WorkVersion` schema;
- [ ] typed `ExternalIdentifier` schema;
- [ ] identity-decision/provenance persistence;
- [ ] DOI normalization/resolution;
- [ ] first Crossref adapter from verified contract;
- [ ] first OpenAlex adapter from verified contract;
- [ ] deterministic high-confidence deduplication rules;
- [ ] ambiguous-match preservation workflow;
- [ ] append-oriented source observation persistence;
- [ ] provider health/rate/backoff layer;
- [ ] Temporal baseline for durable source synchronization/reconciliation;
- [ ] OpenAlex upstream merge/deletion reconciliation model;
- [ ] deterministic provider fixture corpus;
- [ ] replay/idempotency tests;
- [ ] PostgreSQL metadata full-text search projection;
- [ ] baseline metadata/search API;
- [ ] generated OpenAPI/TypeScript-client drift check;
- [ ] CI baseline for backend/web/migrations/workflow replay where applicable.

### Phase 1 exit proof

Given the same observed provider records, NOESYN can deterministically reconstruct canonical identity decisions and explain which observations caused them. Re-ingestion is idempotent, changed source state creates new observations, and external provider IDs remain aliases rather than canonical identity.

---

## Phase 2 — Access Resolver & Rights Engine

**Goal:** find the best lawful access path without yet requiring a complete reader.

Deliverables:

- AccessLocation schema;
- RightsAssertion / License / RightsDecision schemas;
- Unpaywall adapter from verified contract;
- CORE/repository access candidates where the production license gate permits;
- PMC/Europe PMC access signals;
- action-based policy engine;
- user-entitlement placeholder/context model;
- access ranking;
- rights-decision audit/provenance;
- stale/recheck policy;
- negative/unknown-rights tests.

Exit proof:

For a representative test corpus, NOESYN can explain why a work is metadata-only, open-readable, persistable, TTS-eligible, or entitlement-scoped without reducing the decision to a single OA boolean.

## Phase 3 — Artifact Acquisition & Structured Documents

**Goal:** ingest authorized full text into the shared canonical document model.

Deliverables:

- DocumentArtifact registry and hashing;
- secure retrieval worker;
- S3-compatible artifact storage implementation;
- storage/retention classes;
- JATS parser first where available;
- born-digital PDF parser path;
- canonical node tree;
- source anchors;
- reference parsing;
- figures/tables/equation primitives;
- parse-quality scoring;
- parser versioning;
- EvidenceUnit segmentation;
- fixture/evaluation corpus.

Exit proof:

One authorized paper can travel from access resolution to a versioned StructuredDocument and EvidenceUnits with a reconstructible provenance chain.

## Phase 4 — Universal Reader V1

**Goal:** make structured papers genuinely better to read than raw PDFs.

Deliverables:

- responsive reader;
- source/original view;
- section navigation;
- references/citation links;
- figure/table rendering;
- math rendering;
- evidence/source synchronization;
- integrity/version banners;
- formal accessibility baseline;
- private annotations/highlights;
- reading state.

Exit proof:

A user can read an authorized article on desktop/mobile, move from normalized content back to source evidence, and see version/integrity/access state clearly.

## Phase 5 — Paper-Grounded AI V1

**Goal:** trustworthy “Ask this paper” and summaries.

Deliverables:

- evidence retrieval;
- claim-bound answer generation;
- claim/evidence alignment;
- grounding verifier;
- summary modes;
- methods/statistics explanation;
- figure/table explanation where supported;
- source-vs-NOESYN labeling;
- prompt-injection isolation;
- permanent grounding/factuality evaluation suite;
- model/provider abstraction.

Exit proof:

AI answers can be audited at the claim/evidence level and meet defined grounding thresholds on the evaluation corpus.

## Phase 6 — Audio V1

**Goal:** Audible-style single-paper listening.

Deliverables:

- narration plans;
- TTS provider abstraction;
- full narration where rights permit;
- guided paper;
- short/detailed audio summaries;
- math/table/citation speech policies;
- pronunciation layer;
- segmented audio manifest;
- transcript/source synchronization;
- playback state, bookmarks, speed;
- source/evidence action from current audio segment.

Exit proof:

A listener can hear a paper/summary and inspect what type of speech they heard plus its exact source/evidence dependencies.

## Phase 7 — Discovery & Personal Library

**Goal:** turn individual-paper functionality into a daily research product.

Deliverables:

- federated/local scholarly search;
- ranking and filters;
- author/topic/citation exploration;
- saved library;
- collections/playlists;
- research/audio queues;
- notes/highlights sync;
- user knowledge/learning primitives;
- recommendation foundations.

## Phase 8 — Knowledge Graph & Paper Comparison

**Goal:** model relationships among papers and evidence.

Deliverables:

- citation graph;
- integrity graph;
- claim graph;
- support/contradiction/replication candidate edges;
- method/dataset relationship extraction;
- provenance-aware graph explorer;
- two-paper and multi-paper comparison.

## Phase 9 — Ask the Literature / Synthesis

**Goal:** answer research questions across bodies of literature.

Deliverables:

- question decomposition/eligibility planning;
- broad candidate discovery;
- study characterization;
- evidence tables;
- version/integrity-aware inclusion;
- contradiction preservation;
- synthesis with claim-level sources;
- inclusion/exclusion audit trail;
- reproducible synthesis artifacts.

## Phase 10 — Research Learning System

**Goal:** transform literature into persistent learning.

Deliverables:

- prerequisite concept graph;
- adaptive explanation level;
- flashcards;
- personalized learning plans;
- reading/listening comprehension loops;
- cross-paper notes/knowledge graph;
- research-course generation;
- evidence-linked long-form audio briefings.

## Phase 11 — Scale, Institutional & Public API

Potential deliverables:

- corpus-scale official snapshot synchronization;
- incremental source sync at corpus scale;
- specialized search/vector/graph infrastructure when measured needs justify it;
- public/developer API;
- library/institution integrations;
- entitlement federation where legally/contractually appropriate;
- enterprise/private research workspaces;
- advanced observability and SLOs;
- formal compliance/security programs as product scope requires.

## Cross-cutting work that never ends

Every phase includes:

- source contract updates;
- rights/legal review when workflows change;
- security/privacy review;
- provenance/replay tests;
- retraction/correction handling;
- accessibility;
- evaluations;
- cost/performance measurement;
- documentation;
- timestamped engineering logs.

## Decisions intentionally still open after Phase 1A

See `docs/architecture/OPEN_DECISIONS.md` for authority. Major deferred items include:

- production object-store vendor;
- production hosting/cloud provider;
- dedicated search engine beyond PostgreSQL when needed;
- vector engine;
- specialized graph engine;
- AI provider/model;
- TTS provider/model;
- native mobile strategy;
- authentication provider;
- formal accessibility target;
- observability vendor;
- commercial/legal operating policy;
- evaluation-corpus licensing.

These choices must be resolved when their implementation window arrives rather than being silently assumed.