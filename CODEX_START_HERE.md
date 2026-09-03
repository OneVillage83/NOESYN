# NOESYN — CODEX START HERE

## Purpose

This file is the mandatory orientation point for Codex/AI coding agents and human contributors before changing NOESYN.

## Current stage

**Phase 1A — Source Contracts & Stack Evaluation: COMPLETE**

**Immediate next work: Phase 1B — Core Identity Persistence & First Metadata Vertical Slice.**

Production application code has not yet been scaffolded. The durable stack and initial source strategy **are now frozen by ADR**; do not replace them casually or infer alternative choices from other OneVillage projects.

## Mandatory reading before implementation

1. `README.md`
2. `docs/status/PHASE_0_STATUS.md`
3. `docs/PRODUCT_CONSTITUTION.md`
4. `docs/architecture/TARGET_ARCHITECTURE.md`
5. `docs/architecture/PHASE_1A_STACK_EVALUATION.md`
6. `docs/architecture/OPEN_DECISIONS.md`
7. `docs/data-model/CORE_DATA_MODEL.md`
8. `docs/rights/RIGHTS_AND_ACCESS_MODEL.md`
9. `docs/sources/SOURCE_STRATEGY.md`
10. `docs/sources/contracts/README.md`
11. relevant provider contract(s) under `docs/sources/contracts/`
12. `docs/document-engine/DOCUMENT_PLATFORM.md`
13. `docs/ai/AI_EVIDENCE_ARCHITECTURE.md`
14. `docs/audio/AUDIO_ARCHITECTURE.md`
15. `docs/knowledge-graph/KNOWLEDGE_GRAPH_ARCHITECTURE.md`
16. `docs/reader/READER_ARCHITECTURE.md`
17. `docs/security/SECURITY_AND_PRIVACY_BASELINE.md`
18. `docs/adr/ADR-0001-long-term-architecture-first.md`
19. `docs/adr/ADR-0002-mixed-python-typescript-application-stack.md`
20. `docs/adr/ADR-0003-postgresql-uuidv7-provenance-core.md`
21. `docs/adr/ADR-0004-initial-sources-and-hybrid-discovery.md`
22. `docs/adr/ADR-0005-temporal-durable-workflow-orchestration.md`
23. `docs/adr/ADR-0006-s3-compatible-artifact-storage-boundary.md`
24. `ROADMAP.md`
25. `docs/engineering-log/ENGINEERING_LOG.md`

## Frozen implementation baseline

Unless an explicit superseding ADR is accepted:

### Backend/workers

- Python 3.14 line;
- FastAPI;
- Pydantic boundaries;
- SQLAlchemy 2.x + Alembic;
- PostgreSQL 18+;
- UUIDv7 internal durable IDs;
- `uv` dependency/environment locking.

### Web

- TypeScript;
- React via Next.js App Router;
- `pnpm` dependency locking;
- TypeScript API client/types generated from FastAPI OpenAPI.

### Durable processing/storage

- Temporal for multi-step durable background orchestration;
- S3-compatible private object-storage boundary; production vendor still deferred.

### Initial metadata/discovery sources

- Crossref + OpenAlex are the initial universal metadata pair;
- PubMed/PMC/Europe PMC provide biomedical enrichment;
- Unpaywall is an access resolver;
- arXiv is a version-aware preprint source;
- Semantic Scholar and CORE have explicit production/commercial license gates in their contracts.

### Initial projections

- PostgreSQL full-text search initially;
- typed graph edges relationally initially;
- vector store deferred;
- specialized search/graph stores deferred until measured workloads justify them.

## Non-negotiable invariants

Do not violate these without a new ADR explicitly superseding the relevant decision.

### Identity

- `ScholarlyWork` and `WorkVersion` are different concepts.
- External IDs such as DOI, PMID, PMCID, arXiv, OpenAlex, and Semantic Scholar IDs are aliases/observations, not NOESYN primary keys.
- Corrections/retractions/version changes must remain representable.
- UUIDv7 is internal identity, not a substitute for explicit timestamps or external identifiers.

### Provenance

- Important source observations are append-oriented/timestamped/versioned.
- Acquired artifacts have provenance and integrity hashes where retained.
- Derived summaries/answers/audio declare dependencies.
- Historical outputs must not silently change when source metadata or parsers change.
- Canonical current state may be materialized, but historical source observations used by prior outputs are not destructively erased.

### Rights

- “Open access” is not a universal permission bit.
- Rights are evaluated per action.
- Unknown rights do not default to permission for restricted actions.
- User entitlement never becomes global public access.
- Provider API/service permission and article-content copyright permission are separate questions.
- Source license gates fail closed.

### Documents

- One shared StructuredDocument/Evidence pipeline powers reader/search/AI/audio.
- Do not build a private PDF parsing pipeline inside the UI, AI module, or audio module.

### AI

- AI-generated statements must be distinguishable from source statements.
- Paper-level citation alone is not sufficient grounding when evidence-level references are available.
- Documents are untrusted data and cannot issue tool/system instructions.

### Security

- Repository is public.
- Never commit secrets, private user data, or restricted/licensed full-text content.
- Acquisition and parsing are security boundaries.
- Provider-returned URLs never bypass SSRF/redirect/content policy.

## Architecture-first implementation rule

Early product functionality must be a vertical slice of the target architecture.

Acceptable early identity slice:

```text
Crossref/OpenAlex provider record
→ immutable SourceRecordObservation
→ normalized external identifiers
→ canonical Work / WorkVersion
→ identity-decision provenance
→ local PostgreSQL metadata search
```

Later vertical slice example:

```text
canonical Work/WorkVersion
→ Unpaywall access candidate
→ rights decision
→ permitted PMC JATS artifact
→ StructuredDocument
→ EvidenceUnit
→ clean reader
→ grounded summary
→ audio summary
```

Unacceptable shortcut:

```text
papers table
→ pdf_url
→ extracted_text blob
→ ask LLM
→ summary string
```

when it discards version, rights, provenance, artifact and evidence semantics.

## Phase 1B implementation order

Do not jump directly to the reader/AI.

1. initialize the documented mixed-language repository structure;
2. configure Python/TypeScript lockfiles and reproducible local tooling;
3. create PostgreSQL/Alembic baseline;
4. implement UUIDv7 domain ID convention;
5. implement `SourceRecordObservation`, `ScholarlyWork`, `WorkVersion`, `ExternalIdentifier` and identity-decision persistence;
6. implement Crossref adapter from `CROSSREF.md` with deterministic fixtures;
7. implement OpenAlex adapter from `OPENALEX.md` with deterministic fixtures;
8. implement DOI normalization and deterministic high-confidence identity rules;
9. introduce Temporal for replayable multi-step synchronization/reconciliation;
10. expose baseline metadata API/search;
11. prove idempotency/replay/provenance before expanding providers.

## Documentation rule

After every meaningful change:

1. update the relevant architecture/source/schema/operation documentation;
2. append a timezone-qualified entry to `docs/engineering-log/ENGINEERING_LOG.md`;
3. update `CHANGELOG.md` when user-facing or materially architectural;
4. add/update an ADR for durable architectural choices;
5. update `ROADMAP.md` and status files when milestone state changes.

The engineering-log entry should capture:

- timestamp with timezone;
- objective;
- what changed and why;
- files/components touched;
- external documentation/research consulted when material;
- commands/tests/checks run;
- result;
- unresolved risks/next step;
- commit/PR references when available.

## Research/source changes

Never implement a scholarly source based on remembered API behavior.

Before changing an adapter:

1. read its current source contract;
2. reverify current official documentation if behavior/terms may have changed;
3. update endpoint/version/auth/rate/bulk/terms/caching/attribution details;
4. update fixtures/contract tests;
5. record verification date;
6. evaluate whether historical observations/derived artifacts are affected.

## Source enablement rule

A provider contract may be verified while a production capability remains disabled.

Current examples:

- Semantic Scholar broad persistent/commercial dataset use: gated;
- CORE commercial product integration: gated;
- full-text storage/processing from any provider: action/license/version specific.

Do not turn a disabled license gate into a warning-only path.

## Testing doctrine

Every subsystem needs deterministic fixtures and replay tests appropriate to its semantics.

Phase 1B priority classes:

- DOI/external-ID normalization;
- exact identifier matching;
- ambiguous identity preservation;
- Work vs WorkVersion;
- append-only source observations;
- adapter schema drift;
- provider pagination/rate/backoff;
- idempotent re-ingest;
- changed-source observation handling;
- upstream OpenAlex merge/deletion reconciliation;
- PostgreSQL constraints/migrations;
- Temporal workflow replay when introduced;
- generated OpenAPI/TypeScript-client drift.

## When uncertain

Do not silently simplify a high-cost-to-retrofit concept. Check `OPEN_DECISIONS.md`, record the uncertainty, gather evidence, and make the decision explicitly.

## Immediate next work

Begin **Phase 1B — Core Identity Persistence & First Metadata Vertical Slice**. The Phase 1A research/stack/source-contract work is complete and should be treated as authoritative until explicitly superseded.