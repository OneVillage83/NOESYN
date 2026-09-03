# Changelog

All notable NOESYN changes are documented here. Detailed implementation/research notes live in `docs/engineering-log/ENGINEERING_LOG.md`.

## [Unreleased]

### Added — 2026-09-03 — Phase 1A Source Contracts & Stack Evaluation

- Added verified source contracts for Crossref, OpenAlex, Unpaywall, PubMed, PubMed Central, Europe PMC, and arXiv.
- Added Semantic Scholar source contract with an explicit commercial/persistent-data license gate.
- Added CORE source contract with an explicit commercial-product license gate under current CORE terms.
- Selected **Crossref + OpenAlex** as the initial universal metadata pair.
- Selected hybrid local-first discovery: local canonical/source corpus first, permitted provider expansion second, official snapshots/bulk mechanisms at scale.
- Selected mixed **Python 3.14/FastAPI + TypeScript/Next.js** application stack.
- Selected **PostgreSQL 18+** as the canonical transactional store.
- Selected **UUIDv7** as the durable internal NOESYN ID format.
- Selected append-oriented relational source observations/dependency lineage plus materialized current canonical state; full event sourcing rejected.
- Selected PostgreSQL native full-text search as the initial metadata search projection.
- Selected relational typed graph edges initially; specialized graph storage remains deferred.
- Kept vector retrieval technology deferred until Paper-Grounded AI evaluation workloads exist.
- Selected **Temporal** for durable multi-step background orchestration with idempotent Activities and Workflow replay tests.
- Selected an **S3-compatible** private object-storage boundary while deferring the production storage vendor.
- Added `docs/architecture/PHASE_1A_STACK_EVALUATION.md`.
- Added ADR-0002 through ADR-0006 for the durable implementation decisions.
- Reconciled `docs/architecture/OPEN_DECISIONS.md` with accepted decisions and added remaining hosting/auth/observability deferrals.
- Rewrote `docs/sources/SOURCE_STRATEGY.md` to reflect the frozen source roles and fail-closed license gates.
- Updated `docs/sources/contracts/README.md` with the Phase 1A provider status matrix.
- Updated `README.md`, `CODEX_START_HERE.md`, and `ROADMAP.md` so future implementation starts from the accepted Phase 1A baseline.
- Added `docs/status/PHASE_1A_STATUS.md` and marked Phase 1B as the next implementation boundary.

### Phase 1A decisions intentionally still deferred

- production hosting/cloud platform;
- production S3-compatible object-storage vendor;
- dedicated search engine beyond initial PostgreSQL search if later needed;
- vector retrieval engine;
- specialized graph engine;
- AI provider/model;
- TTS provider/model;
- native mobile framework;
- authentication provider;
- observability vendor;
- formal accessibility target;
- permanent evaluation-corpus licensing;
- commercial/legal operating policy beyond the source-specific fail-closed gates already documented.

### Added — 2026-09-03 — Phase 0 Architecture & Governance foundation

- Established NOESYN mission: **From research to understanding.**
- Defined the long-term universal research interface vision.
- Added architecture-first project constitution and ADR.
- Defined canonical scholarly identity separating Work, WorkVersion, DocumentArtifact, StructuredDocument, and EvidenceUnit.
- Defined provenance/lineage requirements for source observations and derived artifacts.
- Defined action-specific rights/access semantics instead of a single open-access boolean.
- Defined source-adapter strategy for Crossref, OpenAlex, Semantic Scholar, Unpaywall, CORE, PubMed/PMC, Europe PMC, arXiv, DOAJ, repositories, and future providers.
- Added source-contract template requiring official-documentation verification before adapter implementation.
- Defined a shared structured-document pipeline for reader, search, AI, and audio.
- Defined evidence-grounded AI architecture with claim/evidence alignment and explicit source-versus-model output classes.
- Defined Audible-style research audio architecture with source/evidence-linked segments.
- Defined scholarly knowledge-graph semantics including citation, integrity, support/contradiction, method, dataset, and derivation relationships.
- Defined universal responsive reader architecture with original-source synchronization.
- Added security/privacy baseline for public metadata, restricted content, user uploads, entitlements, parsers, retrieval, and AI-provider boundaries.
- Added architecture-aligned implementation roadmap.
- Added `CODEX_START_HERE.md` with mandatory contributor/agent rules and documentation discipline.
- Added Phase 0 open-decisions register so unfrozen technology choices remain explicit.
- Added public-repository `.gitignore` protection for secrets, private research artifacts, local databases, and restricted-content directories.
- Initialized detailed timestamped engineering log.

### Phase 0 historical note

Phase 0 intentionally left the implementation stack and initial complementary metadata source unfrozen. Phase 1A has now resolved the durable decisions required to begin implementation; remaining open items are authoritative in `docs/architecture/OPEN_DECISIONS.md`.
