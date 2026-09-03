# NOESYN

> **From research to understanding.**
>
> Long-term vision: **Understand what humanity knows.**

NOESYN is a universal research interface designed to help people discover, lawfully access, read, listen to, understand, compare, and synthesize scholarly research while preserving traceability back to the underlying evidence.

NOESYN is not intended to be a piracy service or a thin wrapper around a single scholarly database. It is designed as a durable research knowledge system that can unify scholarly discovery, access resolution, structured document representation, evidence-grounded AI, audio learning, citation graphs, and personal research workflows.

## Product mission

Build the most useful interface between humans and the scholarly record by making research:

- easier to discover;
- easier to lawfully access;
- easier to read on any device;
- easier to listen to;
- easier to understand at different levels of expertise;
- easier to compare across papers and bodies of literature;
- easier to verify by tracing every derived claim back to its evidence;
- easier to organize into a persistent personal knowledge system.

## Foundational product principle

**NOESYN is designed for the complete long-term architecture first.**

Early implementations may be incomplete vertical slices of that architecture, but the project must not intentionally adopt throwaway architecture that contradicts known long-term requirements simply to ship an MVP faster.

This does not mean implementing every subsystem immediately. It means defining stable boundaries, identities, provenance, rights semantics, and contracts before feature work makes those concepts expensive to retrofit.

## Core invariants

1. **Canonical identity before convenience.** A scholarly work may have many identifiers and versions. NOESYN distinguishes the conceptual work from specific manifestations such as a preprint, accepted manuscript, version of record, correction, or retraction.
2. **Provenance is first-class.** Derived claims, summaries, answers, and audio explanations must be traceable to the source version and, where technically possible, to the exact section, paragraph, table, figure, equation, or other evidence unit that supports them.
3. **Rights are data, not an afterthought.** Discovery, display, storage, redistribution, AI processing, text-to-speech, caching, and commercial reuse are separate permissions and are modeled explicitly.
4. **Discover broadly; reproduce only when authorized.** NOESYN may index metadata for paywalled or restricted works while displaying or processing full text only when rights, user authorization, or source terms allow it.
5. **Source adapters are replaceable.** Crossref, OpenAlex, Semantic Scholar, PubMed/PMC, Europe PMC, CORE, Unpaywall, arXiv, repositories, publishers, and future sources sit behind source-specific adapters rather than leaking source schemas throughout the product.
6. **Evidence-grounded AI by default.** AI output distinguishes source facts, author interpretation, model explanation, uncertainty, and synthesis. Unsupported confidence is not presented as evidence.
7. **Version-aware research.** Corrections, retractions, updates, preprints, and later versions are never collapsed into a single undifferentiated document.
8. **Human-readable and machine-readable are both first-class.** The same canonical research model supports the reader, search, AI, audio, exports, APIs, and future agents.
9. **Accessibility is a product requirement.** Audio, responsive reading, semantic document structure, keyboard navigation, screen-reader compatibility, and comprehensible explanations are architectural concerns, not optional polish.
10. **Reproducibility over rediscovery.** Meaningful design, code, schema, dependency, configuration, source-contract, and operational changes are documented so future work does not repeat prior investigation.

## Long-term capability map

```text
NOESYN
├── Discover
│   ├── universal scholarly search
│   ├── author / institution / venue discovery
│   ├── citation exploration
│   └── topic and semantic discovery
├── Access
│   ├── open-access resolution
│   ├── repository and author-manuscript discovery
│   ├── publisher routing
│   └── user-authorized access paths
├── Read
│   ├── clean responsive reader
│   ├── original PDF / HTML / XML views
│   ├── figures, tables, equations, references
│   └── annotations and notes
├── Listen
│   ├── full-paper narration
│   ├── guided narration
│   ├── short and long summaries
│   └── research learning queues
├── Understand
│   ├── explain at multiple expertise levels
│   ├── methods and statistics explanation
│   ├── figure / table interpretation
│   └── paper-grounded Q&A
├── Analyze
│   ├── claim and evidence extraction
│   ├── study-quality features
│   ├── contradiction and replication signals
│   └── paper comparison
├── Synthesize
│   ├── literature-level questions
│   ├── evidence maps
│   ├── agreement / disagreement analysis
│   └── cited research briefings
└── Learn
    ├── personal library
    ├── playlists / research queues
    ├── notes and highlights
    ├── flashcards and prerequisite concepts
    └── persistent knowledge graph
```

## Provenance chain

```text
Source record
  ↓
Canonical scholarly work
  ↓
Specific version / manifestation
  ↓
Retrieved document artifact
  ↓
Structured document
  ↓
Evidence unit
  ↓
Extracted claim / observation
  ↓
Derived AI statement
  ↓
Summary / answer / comparison / audio explanation
```

NOESYN should be able to answer **“Where did you get that?”** with progressively more precise provenance rather than merely citing a nearby paper.

## Architecture and phase status

### Phase 0 — COMPLETE

Architecture & Governance is an architecture freeze candidate. High-cost-to-retrofit identity, provenance, rights, document/evidence, AI/audio, security and graph semantics were defined before implementation.

See `docs/status/PHASE_0_STATUS.md`.

### Phase 1A — COMPLETE

Source Contracts & Stack Evaluation has frozen the initial implementation baseline and source strategy.

See:

- `docs/architecture/PHASE_1A_STACK_EVALUATION.md`
- `docs/sources/contracts/README.md`
- `docs/architecture/OPEN_DECISIONS.md`
- ADR-0002 through ADR-0006.

### Immediate next phase

**Phase 1B — Core Identity Persistence & First Metadata Vertical Slice.**

No production application code has been scaffolded yet.

## Accepted implementation baseline

### Backend / research workers

- Python 3.14 line;
- FastAPI;
- Pydantic boundaries;
- SQLAlchemy 2.x + Alembic;
- PostgreSQL 18+;
- UUIDv7 durable internal IDs;
- `uv` dependency/environment locking.

### Web

- TypeScript;
- React via Next.js App Router;
- `pnpm` dependency locking;
- TypeScript API client generated from FastAPI OpenAPI.

### Durable workflows / artifacts

- Temporal for multi-step durable background orchestration;
- S3-compatible private object-storage interface; production storage vendor remains deferred.

### Initial scholarly-source strategy

- **Crossref + OpenAlex** — initial universal metadata pair;
- **PubMed / PMC / Europe PMC** — biomedical bibliography, identity and permitted structured/full-text enrichment;
- **Unpaywall** — DOI access/OA-location resolver;
- **arXiv** — version-aware preprint source;
- **Semantic Scholar** — strategic citation/recommendation enrichment with a commercial-use gate for broad persistent production data use;
- **CORE** — strategic repository/OA/full-text discovery with a commercial-license gate for production product use.

### Initial projections

- PostgreSQL full-text search initially;
- graph edges in provenance-aware relational tables initially;
- dedicated search/vector/graph stores deferred until measured workloads justify them.

## Architectural documents

Start with:

- `docs/PRODUCT_CONSTITUTION.md`
- `docs/architecture/TARGET_ARCHITECTURE.md`
- `docs/architecture/PHASE_1A_STACK_EVALUATION.md`
- `docs/data-model/CORE_DATA_MODEL.md`
- `docs/rights/RIGHTS_AND_ACCESS_MODEL.md`
- `docs/sources/SOURCE_STRATEGY.md`
- `docs/sources/contracts/README.md`
- `docs/document-engine/DOCUMENT_PLATFORM.md`
- `docs/ai/AI_EVIDENCE_ARCHITECTURE.md`
- `docs/audio/AUDIO_ARCHITECTURE.md`
- `docs/knowledge-graph/KNOWLEDGE_GRAPH_ARCHITECTURE.md`
- `docs/security/SECURITY_AND_PRIVACY_BASELINE.md`
- `docs/adr/`
- `ROADMAP.md`
- `CODEX_START_HERE.md`
- `CHANGELOG.md`
- `docs/engineering-log/ENGINEERING_LOG.md`

## Documentation rule

Every meaningful change must leave a durable record. At minimum, changes that affect architecture, schema, dependencies, source behavior, legal/rights assumptions, configuration, infrastructure, security, data semantics, AI behavior, or operational procedures must update the appropriate documentation and add a timestamped engineering-log entry.

A future contributor should not have to repeat an investigation simply because its result existed only in chat history, terminal output, or memory.

## Repository safety

This repository is public. Never commit:

- API keys or access tokens;
- publisher or institutional credentials;
- `.env` files containing secrets;
- private user documents;
- licensed full-text content that cannot legally be redistributed;
- production database exports containing private or restricted data.

Secret handling and rights enforcement are architecture requirements from the first implementation commit.

## Current stage

**Phase 1A is complete. Phase 1B is next.**

The implementation stack and initial metadata source pair are now durable ADR-backed decisions. Remaining open choices are tracked in `docs/architecture/OPEN_DECISIONS.md` and must not be silently decided by convenience.