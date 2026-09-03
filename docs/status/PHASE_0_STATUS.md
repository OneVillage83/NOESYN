# NOESYN Phase 0 Status

- **Phase:** 0 — Architecture & Governance
- **Status:** COMPLETE — ARCHITECTURE FREEZE CANDIDATE
- **Date:** 2026-09-03
- **Production code status:** Not started

## What Phase 0 accomplished

Phase 0 established the long-term system shape before implementation stack or application code could lock NOESYN into a thin paper/PDF model.

Completed foundations:

- product constitution and long-term mission;
- architecture-first ADR;
- target bounded contexts;
- Work / WorkVersion / Artifact / StructuredDocument / Evidence identity hierarchy;
- append-oriented source observation and provenance requirements;
- access-resolution and action-specific rights model;
- scholarly source/adaptor strategy;
- shared document/evidence platform;
- evidence-grounded AI architecture;
- Audible-style research audio architecture;
- reader architecture;
- scholarly knowledge graph semantics;
- security/privacy baseline;
- source-contract discipline;
- architecture-aligned roadmap;
- explicit open-decisions register;
- public-repository secret/content safeguards;
- `CODEX_START_HERE.md` contributor rules;
- changelog and timestamped engineering log.

## Phase 0 invariants now frozen

These should not change without explicit architectural review/ADR:

1. A scholarly Work is not the same entity as a WorkVersion.
2. External provider identifiers are aliases/observations rather than NOESYN primary keys.
3. Source observations and important transformations retain provenance.
4. Retrieved bytes/artifacts are distinct from normalized structured documents.
5. Reader, search, AI, and audio share one StructuredDocument/Evidence pipeline.
6. Rights are evaluated per requested action, not as one `is_open_access` bit.
7. User/institutional entitlement remains scoped and never creates global access.
8. Derived summaries, answers, comparisons, and audio retain dependency lineage.
9. Retractions/corrections/version differences remain first-class.
10. Machine-inferred claims/relationships remain distinguishable from source-declared facts.
11. Early releases are vertical slices of the target architecture, not throwaway MVP architecture.
12. Meaningful work must update durable documentation and the engineering log.

## What is intentionally not frozen

The following are implementation decisions, not missing architecture:

- backend language/framework;
- frontend framework;
- relational database vendor;
- internal opaque ID format;
- object storage;
- background queue/orchestrator;
- metadata search engine;
- vector retrieval engine;
- graph storage engine;
- AI provider/model;
- TTS provider/model;
- hosting/cloud platform;
- exact initial complementary metadata provider alongside Crossref;
- mobile implementation strategy.

These are tracked in `docs/architecture/OPEN_DECISIONS.md` and must be resolved with evidence/ADRs.

## Known pre-production obligations

Architecture completion does **not** imply launch readiness.

Before relevant production workflows:

- dedicated provider contracts must be verified against official documentation;
- stack decisions must be recorded through ADRs;
- legal review is required before commercial-scale restricted full-text/user-entitlement/derivative distribution workflows;
- permanent evaluation fixtures must have clear storage/reuse rights;
- production security, privacy, accessibility, and operational controls must be implemented and tested.

## Next phase

### Phase 1A — Source Contracts & Stack Evaluation

Recommended order:

1. Verify and write source contracts for Crossref, OpenAlex, Semantic Scholar, Unpaywall, PMC, Europe PMC, CORE, and arXiv.
2. Evaluate backend/database/ID/queue/storage candidates against the target architecture.
3. Record selected durable technologies in ADRs.
4. Define concrete Phase 1 schemas/migrations from the conceptual core data model.
5. Initialize repository application/package structure.
6. Implement the first metadata identity vertical slice with deterministic fixtures, idempotency, provenance, and replay tests.

## Freeze-candidate meaning

“Architecture freeze candidate” means the high-cost-to-retrofit semantics identified in Phase 0 are explicitly modeled and can now serve as the basis for implementation. It does not mean the architecture can never evolve. Future changes must be deliberate, documented, and evaluated against existing invariants and accumulated data/provenance.
