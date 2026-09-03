# NOESYN Open Decisions Register

## Purpose

Track material decisions that are intentionally **not** frozen yet. An unresolved item must not become an implicit architecture decision simply because one implementation happens to use a particular technology or assumption first.

Status values:

- `OPEN`
- `RESEARCHING`
- `PROPOSED`
- `DECIDED` — must link to an ADR or authoritative design document
- `DEFERRED`

## OD-001 — Primary backend language/framework

- **Status:** OPEN
- **Decision window:** before Phase 1 production implementation
- **Candidates to evaluate:** Python-centric, TypeScript-centric, or mixed architecture
- **Criteria:** scholarly/API ecosystem, parsing/ML ecosystem, async/background jobs, type safety, performance, contributor velocity, operations.

## OD-002 — Primary relational database

- **Status:** OPEN
- **Likely requirement:** strong relational constraints, JSON support, full transactional semantics, migrations, good operational ecosystem.
- **Note:** do not let vector/graph requirements force core canonical relational entities into a specialized store.

## OD-003 — Initial metadata source pair

- **Status:** OPEN
- **Decision window:** Phase 1 source-contract evaluation
- **Baseline:** Crossref is a strong DOI metadata candidate; select complementary broad graph source after exact current contract review (OpenAlex and/or Semantic Scholar).
- **Criteria:** breadth, freshness, identifiers, citation graph, API/bulk terms, rate, reproducibility, licensing/attribution.

## OD-004 — Initial searchable corpus strategy

- **Status:** OPEN
- **Question:** federated API search first, locally synchronized metadata index first, or hybrid?
- **Constraint:** architecture must permit transition to provider bulk snapshots/incremental sync without changing canonical identity semantics.

## OD-005 — Search engine

- **Status:** DEFERRED
- **Decision window:** before local full-text/large metadata search requires it.
- **Candidates:** PostgreSQL search initially, dedicated engine later, or other evaluated option.

## OD-006 — Vector retrieval technology

- **Status:** DEFERRED
- **Rule:** embeddings/vector index are rebuildable projections; they are not canonical evidence storage.

## OD-007 — Graph storage technology

- **Status:** DEFERRED
- **Rule:** define graph semantics first. Relational edge tables may be sufficient until traversal workloads justify specialized graph infrastructure.

## OD-008 — Object storage

- **Status:** OPEN
- **Criteria:** private-by-default ACLs, signed URLs, lifecycle policies, hashes/metadata, local-development parity, cost.

## OD-009 — Queue/background orchestration

- **Status:** OPEN
- **Needs:** source sync, artifact acquisition, parsing, AI generation, audio rendering, integrity reprocessing, retry/idempotency.

## OD-010 — Frontend framework

- **Status:** OPEN
- **Criteria:** complex reader, mobile responsiveness, accessibility, PDF/source synchronization, audio state, streaming AI, maintainability.

## OD-011 — Native mobile strategy

- **Status:** DEFERRED
- **Options:** responsive/PWA first, cross-platform native, or platform-native.
- **Constraint:** audio queues/offline features may eventually justify deeper native support.

## OD-012 — AI provider/model strategy

- **Status:** DEFERRED until AI implementation phase
- **Rule:** task-level abstraction and evaluation gates come before provider lock-in.
- **Criteria:** grounding quality, structured outputs, context, multimodality, privacy, cost, latency, model/version stability.

## OD-013 — TTS provider/model strategy

- **Status:** DEFERRED until audio phase
- **Criteria:** naturalness, scientific pronunciation, SSML/control, segment timing, cost, licensing, privacy, caching rights, voice availability.

## OD-014 — Public product domain/branding assets

- **Status:** OPEN
- **Note:** product name is NOESYN; domain/trademark clearance is separate from repository architecture.

## OD-015 — Commercial/legal operating policy

- **Status:** OPEN / requires qualified review before relevant launch
- **Questions:** detailed treatment of TDM, user-entitled content, derivative summaries/audio, publisher/institution integrations, DMCA/takedown process, source-specific terms.

## OD-016 — Formal accessibility target

- **Status:** OPEN
- **Decision window:** before production reader UI freeze
- **Likely baseline:** current WCAG AA target at implementation time, verified from official standard before ADR.

## OD-017 — Canonical internal ID format

- **Status:** OPEN
- **Options:** UUIDv7, ULID, other sortable opaque identifiers.
- **Rule:** do not expose sequential database IDs as durable public identity by accident.

## OD-018 — Event/provenance persistence model

- **Status:** OPEN
- **Question:** append-only relational tables, event log, or hybrid.
- **Constraint:** source observations and important derived-artifact dependencies must be replay/audit capable.

## OD-019 — Full-text retention default

- **Status:** OPEN
- **Rule:** retention is rights/action-specific. Technical storage architecture must support public reusable, limited, entitlement-scoped, transient-only, and metadata-only classes.

## OD-020 — Evaluation corpus licensing

- **Status:** OPEN
- **Need:** permanent legal-to-store fixtures spanning JATS/PDF/layout/math/table/figure/version/integrity cases.

## Closing rule

When an open decision is resolved:

1. create an ADR if the choice is durable/architectural;
2. update this register with `DECIDED` and the ADR reference;
3. update affected architecture docs;
4. append the engineering log;
5. add tests/fixtures proving critical assumptions where applicable.
