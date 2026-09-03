# NOESYN Roadmap

## Roadmap doctrine

Implementation phases are vertical slices of the target architecture. Phase numbers describe build order, not permanent product boundaries.

Every phase must update documentation and the timestamped engineering log.

## Phase 0 — Architecture & Governance

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
- [ ] freeze implementation stack after Phase 1 source-contract/technology evaluation;
- [ ] qualified legal review before workflows involving commercial-scale restricted full-text processing or redistribution.

### Phase 0 exit condition

The architecture is a freeze candidate when all known high-cost-to-retrofit concepts are explicitly modeled and open implementation choices are documented rather than silently assumed.

## Phase 1 — Source Contracts & Core Identity

**Goal:** prove canonical identity and source adapters with metadata only before full-text complexity.

Deliverables:

- source contracts for initial providers;
- provider-neutral SourceRecordObservation schema;
- canonical Work / WorkVersion / ExternalIdentifier schema;
- DOI normalization and resolution;
- PMID/PMCID/arXiv/provider identifier crosswalks;
- deterministic and confidence-based deduplication;
- first metadata adapters (recommended initial set: Crossref + OpenAlex or Semantic Scholar, with exact choice frozen after contract review);
- append-oriented source observation persistence;
- replay/idempotency tests;
- source fixture corpus;
- provider health/rate/backoff layer;
- baseline search API over normalized metadata.

Exit proof:

Given the same observed provider records, NOESYN can deterministically reconstruct canonical identity decisions and explain which observations caused them.

## Phase 2 — Access Resolver & Rights Engine

**Goal:** find the best lawful access path without yet requiring a complete reader.

Deliverables:

- AccessLocation schema;
- RightsAssertion / License / RightsDecision schemas;
- Unpaywall adapter/contract;
- CORE/repository access candidates as appropriate;
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
- accessibility baseline;
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

- bulk scholarly metadata ingestion;
- incremental source sync at corpus scale;
- specialized search/vector/graph infrastructure;
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

## What is intentionally not frozen in Phase 0

- frontend framework;
- backend language/framework;
- relational database vendor;
- object store vendor;
- search engine;
- vector engine;
- graph engine;
- queue/orchestration technology;
- AI provider/model;
- TTS provider/model;
- hosting/cloud provider.

Those choices must be evaluated against the architecture rather than chosen first and allowed to define it.
