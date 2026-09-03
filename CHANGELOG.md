# Changelog

All notable NOESYN changes are documented here. Detailed implementation/research notes live in `docs/engineering-log/ENGINEERING_LOG.md`.

## [Unreleased]

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

### Intentionally not frozen

The Phase 0 pass does not yet select the frontend/backend framework, primary database, search/vector/graph engines, AI/TTS providers, queue/orchestration technology, object store, hosting provider, or first complementary broad metadata source. Those decisions require explicit evaluation and ADRs rather than accidental lock-in.
