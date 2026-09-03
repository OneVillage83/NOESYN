# NOESYN Engineering Log

## Logging standard

This file is the durable chronological record of meaningful NOESYN work.

Every future entry should include:

- timestamp with timezone;
- phase/workstream;
- objective;
- what changed;
- why;
- files/components touched;
- external documentation/research consulted when material;
- commands/tests/checks performed;
- result;
- unresolved risks/decisions;
- commit/PR references when available.

Do not rely on chat history, terminal scrollback, or memory as the only record of an architectural or implementation decision.

---

## 2026-09-03T01:01:47-07:00 — Phase 0 Architecture & Governance foundation

### Objective

Initialize the previously empty `OneVillage83/NOESYN` repository as a production-oriented architecture/governance foundation for the long-term universal research interface before implementation code or stack choices are introduced.

### Product direction frozen

NOESYN is defined as a **universal research interface and evidence-grounded knowledge system**, not merely a research-paper search site or PDF summarizer.

Long-term user loop:

```text
Question
→ discover scholarly record
→ resolve canonical work/version
→ find best lawful access
→ acquire/parse permitted document
→ identify evidence
→ read / ask / compare / synthesize
→ listen / learn
→ retain personal research knowledge
```

Tagline adopted for repository documentation:

> **From research to understanding.**

Long-term vision statement:

> **Understand what humanity knows.**

### Architectural doctrine frozen

Accepted ADR-0001: **Design for the Long-Term Architecture First.**

Early features must be vertical slices of the target architecture rather than throwaway MVP structures that collapse papers into a PDF URL/text blob/summary.

High-cost-to-retrofit concepts explicitly preserved from the start:

- ScholarlyWork vs WorkVersion;
- external identifiers vs internal canonical IDs;
- source observations/provenance;
- exact acquired artifact identity;
- structured document versioning;
- evidence units;
- claim/evidence dependencies;
- action-specific rights decisions;
- user-scoped entitlements;
- corrections/retractions/version lineage;
- public vs restricted/private content boundaries.

### Repository changes

#### Commit `8c1accbbcfb97c0bf0c64cb2ffa20be72cb729cf`

Created `README.md` as the initial repository commit.

Recorded:

- mission;
- long-term capability map;
- project invariants;
- provenance chain;
- architecture-first rule;
- public-repository safety rules;
- Phase 0 documentation map;
- requirement to document meaningful future changes.

#### Commit `594f7d858374a129c9e668111ef2d938eb14dce3`

Created `.gitignore`.

Added protections against accidental commits of:

- `.env`/secrets/credentials;
- private keys;
- local databases;
- private user uploads;
- paper/full-text/PDF directories;
- restricted audio artifacts;
- normal language/tool build artifacts.

Reason: repository is public and NOESYN will eventually interact with licensed/restricted research content and credentials.

#### Commit `ce26db55fd2fd295534de68806102a99640154b2`

Created `docs/PRODUCT_CONSTITUTION.md`.

Formalized:

- product promises;
- non-goals;
- architecture doctrine;
- accessibility requirement;
- version-awareness;
- AI uncertainty/inspectability;
- definition of product success.

Explicit non-goal: NOESYN is not intended to operate as a Sci-Hub clone or redistribute copyrighted full text without authorization.

#### Commit `cb78a3100e4ab8c9a4b5d6191669f2429142e2f7`

Created `docs/architecture/TARGET_ARCHITECTURE.md`.

Defined bounded contexts:

- Discovery;
- Identity;
- Source Integration;
- Access Resolution;
- Rights Engine;
- Document Acquisition;
- Document Platform;
- Evidence Platform;
- AI Research Layer;
- Audio Platform;
- Knowledge Graph;
- User Research Domain.

Storage technology intentionally left unfrozen. Document notes that a modular monolith may implement logical boundaries before microservices are justified.

#### Commit `e68fedcadb34c2bb708cd5cd7492dbaf539b6c9b`

Created `docs/data-model/CORE_DATA_MODEL.md`.

Defined conceptual entities including:

- ScholarlyWork;
- ExternalIdentifier;
- WorkVersion;
- SourceRecordObservation;
- Person/Authorship;
- Organization/Venue;
- AccessLocation;
- RightsAssertion/License;
- DocumentArtifact;
- StructuredDocument/DocumentNode;
- EvidenceUnit;
- CitationEdge;
- Claim/ClaimEvidenceLink;
- DerivedArtifact/Dependency;
- integrity events;
- user-domain entities.

Critical invariant: external provider IDs are never NOESYN canonical primary keys.

#### Commit `d36edfc5a0d77061aed157a143e38ca012eaabbf`

Created `docs/rights/RIGHTS_AND_ACCESS_MODEL.md`.

Defined separate actions for discovery, retrieval, persistence, display, text mining, embeddings, summarization, explanation, TTS, derivatives, redistribution, and commercial use.

Defined decision states:

- ALLOW;
- DENY;
- UNKNOWN;
- CONDITIONAL.

Established default-deny behavior for actions requiring affirmative rights when evidence is unknown.

Established entitlement scoping: a user's institutional/publisher/purchased access does not convert a work into globally open content.

#### Commit `14d186e74937b5ef31d8b224923ab8fee156d9a3`

Created `docs/sources/SOURCE_STRATEGY.md`.

Defined source classes and initial candidate integrations:

- Crossref;
- OpenAlex;
- Semantic Scholar;
- PubMed/PMC;
- Europe PMC;
- CORE;
- Unpaywall;
- arXiv;
- DOAJ;
- institutional/subject repositories;
- publishers where permitted.

Established source-adapter contract and rule that provider records are observations rather than canonical truth.

#### Commit `9a40c427b98456355679216df8d4f9c6a4b2dfc3`

Created `docs/document-engine/DOCUMENT_PLATFORM.md`.

Defined one shared artifact → structured document → evidence pipeline for reader/search/AI/audio.

Established:

- source anchors;
- parser versioning;
- structured XML preference where legally available;
- PDF quality/confidence handling;
- table/figure/math/reference representations;
- evidence segmentation;
- fixture/evaluation requirements.

Critical invariant: UI/AI/audio may not build independent private paper parsers.

#### Commit `632cd14839e488767c1c41c0d7bed6331ae4e923`

Created `docs/ai/AI_EVIDENCE_ARCHITECTURE.md`.

Defined:

- source extraction vs author interpretation vs NOESYN explanation/synthesis/inference;
- claim-level evidence alignment;
- paper-grounded Q&A pipeline;
- literature-synthesis pipeline;
- study characterization;
- integrity-aware synthesis;
- numeric-fidelity requirements;
- model/provider abstraction;
- prompt-injection boundary;
- permanent evaluation dimensions.

Critical invariant: NOESYN is not “RAG over PDFs.”

#### Commit `013b85415451219159dd7afa6fd1d4528ea99d28`

Created `docs/audio/AUDIO_ARCHITECTURE.md`.

Defined:

- full-paper narration where rights permit;
- guided-paper mode;
- summary audio;
- section playback;
- future literature briefings;
- narration plans;
- source/generated-speech distinction;
- citation/math/table/figure speech policy;
- pronunciation layer;
- segmented manifests and source/evidence synchronization;
- rights inheritance for audio artifacts.

#### Commit `694c6cda97cf4d68056dd469a100264d8e0bfc4d`

Created `docs/knowledge-graph/KNOWLEDGE_GRAPH_ARCHITECTURE.md`.

Defined provenance-aware graph semantics for:

- work/version identity;
- citations;
- corrections/retractions;
- support/contradiction/replication;
- methods/datasets;
- evidence/derived artifacts.

Critical rule: `CITES` never automatically means `SUPPORTS`.

#### Commit `ed3ac679bc4073773e84c461fb5ae449e57ce5e6`

Created `docs/reader/READER_ARCHITECTURE.md`.

Defined responsive reader modes and original-source synchronization, evidence navigation, explain interactions, figures/tables/math, annotations, version comparison, accessibility, integrity warnings, and rights-aware offline behavior.

#### Commit `65be6ebd0d01eba1a306c3e61f64f14b558e0a24`

Created `docs/security/SECURITY_AND_PRIVACY_BASELINE.md`.

Defined separate security domains for public metadata, reusable content, restricted content, entitlement-scoped content, user documents/state, credentials, and derivatives.

Added requirements for:

- secret handling;
- authorization;
- private object storage;
- vector/search isolation;
- AI-provider privacy;
- document prompt injection;
- SSRF-safe acquisition;
- malicious file uploads;
- parser sandboxing;
- XML/XXE safety;
- web security;
- privacy/data lifecycle;
- supply-chain security;
- incident readiness.

#### Commit `822324df941ed24d3bf4bf74ce58370e0060adcb`

Created `docs/adr/ADR-0001-long-term-architecture-first.md` and formally accepted the architecture-first implementation strategy.

#### Commit `a7ddc197ef842bac58cf1df3a1ff5c18861c3ab7`

Created `ROADMAP.md`.

Defined staged vertical-slice roadmap:

- Phase 0 Architecture & Governance;
- Phase 1 Source Contracts & Core Identity;
- Phase 2 Access Resolver & Rights Engine;
- Phase 3 Artifact Acquisition & Structured Documents;
- Phase 4 Universal Reader V1;
- Phase 5 Paper-Grounded AI V1;
- Phase 6 Audio V1;
- Phase 7 Discovery & Personal Library;
- Phase 8 Knowledge Graph & Paper Comparison;
- Phase 9 Ask the Literature / Synthesis;
- Phase 10 Research Learning System;
- Phase 11 Scale, Institutional & Public API.

#### Commit `196f5de25c6e7ef67e416759a2eb21cfc1e4472e`

Created `CODEX_START_HERE.md`.

Made architecture/docs reading mandatory before implementation changes and required engineering-log/changelog/ADR/roadmap maintenance after meaningful work.

#### Commit `bd00c17767344c605eb757125936ab5f417f77dd`

Created `docs/architecture/OPEN_DECISIONS.md`.

Explicitly left unfrozen:

- backend/framework;
- database;
- initial complementary metadata source;
- search strategy/engine;
- vector/graph technology;
- object storage;
- queues;
- frontend/mobile strategy;
- AI/TTS provider;
- domain/brand clearance;
- legal operating policy;
- accessibility target;
- internal ID format;
- event/provenance persistence;
- retention defaults;
- evaluation-corpus licensing.

Reason: prevent accidental lock-in.

#### Commit `92dc6ae498d8e396f30e3baee392b925823d032d`

Created `docs/sources/contracts/README.md`.

Established the mandatory template for provider contracts, including official documentation, terms, identifiers, operations, authentication, rate limits, pagination, freshness, bulk strategy, caching, rights fields, attribution, mapping, failures, fixtures, security, and verification timestamp.

#### Commit `1bd3446e1fed34d365de0c1c5897605d38d8bf77`

Created `CHANGELOG.md` and recorded the complete Phase 0 foundation as the first unreleased architecture release.

### External source research performed

Current provider constraints were checked against official/current documentation during Phase 0 planning.

#### Crossref

Verified:

- public REST metadata access;
- public/polite/Metadata Plus access models;
- recommendation to identify requests and cache responsibly;
- availability of public data files/snapshots for bulk use.

Consulted:

- https://www.crossref.org/documentation/retrieve-metadata/rest-api/
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/access-and-authentication/
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/tips-for-using-public-data-files-and-plus-snapshots/

#### Semantic Scholar

Verified current API families documented as:

- Academic Graph;
- Recommendations;
- Datasets.

Consulted:

- https://www.semanticscholar.org/product/api

#### Unpaywall

Verified:

- REST API v2;
- requests require an email parameter;
- published guidance to limit API usage to 100,000 calls/day;
- database snapshot recommended for higher-scale access.

Consulted:

- https://unpaywall.org/products/api

#### PubMed Central

Verified important rights/retrieval constraints:

- not all PMC content is reusable/text-mineable;
- licenses vary by article;
- automated retrieval must use approved PMC developer services;
- current OAI-PMH endpoint is the modernized `https://pmc.ncbi.nlm.nih.gov/api/oai/v1/mh/` endpoint;
- OAI-PMH full text is limited to content whose licenses/usage rights allow reuse.

Consulted:

- https://pmc.ncbi.nlm.nih.gov/tools/developers/
- https://pmc.ncbi.nlm.nih.gov/tools/oai/

#### CORE

Verified CORE provides machine access to OA metadata/full text across its provider network and publishes explicit API access/rate/terms information.

Consulted:

- https://core.ac.uk/services/api

#### Europe PMC

Verified developer APIs/OAI/bulk mechanisms for metadata and OA content, with different availability depending on collection/content rights.

Consulted:

- https://europepmc.org/developers

### Checks performed

- Confirmed repository existed and was initially empty.
- Confirmed default branch `main` and public visibility.
- Confirmed Phase 0 files were created successfully on `main` through GitHub writes.
- No production code exists yet, therefore no runtime/unit tests were applicable in this pass.

### Key risks intentionally not resolved yet

1. Exact technology stack remains open.
2. Each external source still requires a dedicated VERIFIED source contract before production adapter code.
3. OpenAlex current exact API/auth/rate/bulk contract still needs dedicated official-source verification in Phase 1.
4. arXiv current API/bulk/content-license contract still needs dedicated official-source verification in Phase 1.
5. DOAJ contract still needs dedicated verification.
6. Legal review is required before commercial-scale workflows involving restricted full text, user institutional access, or broad derivative distribution.
7. A permanent evaluation corpus with clear rights to store/use must be selected before parser/AI evaluation work.

### Result

Phase 0 repository foundation is substantially complete. The project now has a documented long-term architecture, core invariants, rights model, provenance model, source strategy, reader/AI/audio/graph architecture, security baseline, roadmap, contributor rules, and open-decision process before any production implementation code is written.

### Next recommended work

Begin **Phase 1A — Source Contracts & Stack Evaluation**:

1. create VERIFIED source contracts for Crossref, OpenAlex, Semantic Scholar, Unpaywall, PMC, Europe PMC, CORE, and arXiv;
2. compare implementation-stack candidates against target architecture;
3. create ADRs for the selected backend/database/ID strategy;
4. only then initialize application/package structure;
5. implement the first metadata identity vertical slice with deterministic fixtures and replay tests.
