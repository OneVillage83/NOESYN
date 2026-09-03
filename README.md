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

1. **Canonical identity before convenience.** A scholarly work may have many identifiers and versions. NOESYN must distinguish the conceptual work from specific manifestations such as a preprint, accepted manuscript, version of record, correction, or retraction.
2. **Provenance is first-class.** Derived claims, summaries, answers, and audio explanations must be traceable to the source version and, where technically possible, to the exact section, paragraph, table, figure, equation, or other evidence unit that supports them.
3. **Rights are data, not an afterthought.** Discovery, display, storage, redistribution, AI processing, text-to-speech, caching, and commercial reuse are separate permissions and must be modeled explicitly.
4. **Discover broadly; reproduce only when authorized.** NOESYN may index metadata for paywalled or restricted works while displaying or processing full text only when rights, user authorization, or source terms allow it.
5. **Source adapters are replaceable.** Crossref, OpenAlex, Semantic Scholar, PubMed/PMC, Europe PMC, CORE, Unpaywall, arXiv, repositories, publishers, and future sources must sit behind source-specific adapters rather than leaking source schemas throughout the product.
6. **Evidence-grounded AI by default.** AI output should distinguish source facts, author interpretation, model explanation, uncertainty, and synthesis. Unsupported confidence must not be presented as evidence.
7. **Version-aware research.** Corrections, retractions, updates, preprints, and later versions must never be collapsed into a single undifferentiated document.
8. **Human-readable and machine-readable are both first-class.** The same canonical research model should support the reader, search, AI, audio, exports, APIs, and future agents.
9. **Accessibility is a product requirement.** Audio, responsive reading, semantic document structure, keyboard navigation, screen-reader compatibility, and comprehensible explanations are architectural concerns, not optional polish.
10. **Reproducibility over rediscovery.** Meaningful design, code, schema, dependency, configuration, source-contract, and operational changes must be documented so future work does not repeat prior investigation.

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

The target lineage is:

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

## Phase 0 status

Phase 0 establishes architecture and governance before production implementation. See:

- `docs/PRODUCT_CONSTITUTION.md`
- `docs/architecture/TARGET_ARCHITECTURE.md`
- `docs/data-model/CORE_DATA_MODEL.md`
- `docs/rights/RIGHTS_AND_ACCESS_MODEL.md`
- `docs/sources/SOURCE_STRATEGY.md`
- `docs/ai/AI_EVIDENCE_ARCHITECTURE.md`
- `docs/audio/AUDIO_ARCHITECTURE.md`
- `docs/knowledge-graph/KNOWLEDGE_GRAPH_ARCHITECTURE.md`
- `docs/security/SECURITY_AND_PRIVACY_BASELINE.md`
- `docs/adr/ADR-0001-long-term-architecture-first.md`
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

**Phase 0 — Architecture & Governance**

No production stack is frozen yet. Technology choices should follow the domain boundaries and invariants documented during Phase 0 rather than defining them prematurely.
