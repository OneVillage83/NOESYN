# NOESYN Open Decisions Register

## Purpose

Track material decisions that are intentionally **not** frozen yet. An unresolved item must not become an implicit architecture decision simply because one implementation happens to use a particular technology or assumption first.

Status values:

- `OPEN`
- `RESEARCHING`
- `PROPOSED`
- `DECIDED` — must link to an ADR or authoritative design document
- `DEFERRED`

---

## OD-001 — Primary backend language/framework

- **Status:** DECIDED
- **Decision:** Python 3.14 line + FastAPI backend/workers; TypeScript + Next.js web application.
- **Authority:** `docs/adr/ADR-0002-mixed-python-typescript-application-stack.md`

## OD-002 — Primary relational database

- **Status:** DECIDED
- **Decision:** PostgreSQL 18+ canonical transactional store.
- **Authority:** `docs/adr/ADR-0003-postgresql-uuidv7-provenance-core.md`

## OD-003 — Initial metadata source pair

- **Status:** DECIDED
- **Decision:** Crossref + OpenAlex.
- **Authority:** `docs/adr/ADR-0004-initial-sources-and-hybrid-discovery.md`
- **Notes:** Semantic Scholar and CORE remain useful but commercial/bulk production use is controlled by their source-contract license gates.

## OD-004 — Initial searchable corpus strategy

- **Status:** DECIDED
- **Decision:** hybrid local-first canonical/source corpus with permitted federated candidate expansion; progressively adopt official source snapshots/bulk synchronization.
- **Authority:** `docs/adr/ADR-0004-initial-sources-and-hybrid-discovery.md`

## OD-005 — Search engine

- **Status:** DECIDED FOR INITIAL PHASES
- **Decision:** PostgreSQL native full-text/indexed metadata search initially.
- **Authority:** `docs/adr/ADR-0003-postgresql-uuidv7-provenance-core.md`, `docs/adr/ADR-0004-initial-sources-and-hybrid-discovery.md`
- **Review trigger:** measured relevance/scale/latency requirements exceeding PostgreSQL search.
- **Constraint:** any dedicated engine is a rebuildable projection, never canonical scholarly/evidence state.

## OD-006 — Vector retrieval technology

- **Status:** DEFERRED
- **Decision window:** Paper-Grounded AI implementation/evaluation phase.
- **Rule:** embeddings/vector index are rebuildable projections; they are not canonical evidence storage.

## OD-007 — Graph storage technology

- **Status:** DECIDED FOR INITIAL PHASES
- **Decision:** typed provenance-aware graph edges in PostgreSQL relational tables initially; specialized graph engine deferred until measured traversal workloads justify it.
- **Authority:** `docs/adr/ADR-0003-postgresql-uuidv7-provenance-core.md`

## OD-008 — Object storage

- **Status:** DECIDED AT INTERFACE LEVEL / VENDOR DEFERRED
- **Decision:** S3-compatible object-storage boundary, private by default, with signed access, lifecycle policy and content-hash metadata.
- **Production vendor:** DEFERRED.
- **Authority:** `docs/adr/ADR-0006-s3-compatible-artifact-storage-boundary.md`

## OD-009 — Queue/background orchestration

- **Status:** DECIDED
- **Decision:** Temporal durable workflows/Activities for multi-step background orchestration.
- **Authority:** `docs/adr/ADR-0005-temporal-durable-workflow-orchestration.md`

## OD-010 — Frontend framework

- **Status:** DECIDED
- **Decision:** TypeScript + React via Next.js App Router; generated TypeScript API client from FastAPI OpenAPI.
- **Authority:** `docs/adr/ADR-0002-mixed-python-typescript-application-stack.md`

## OD-011 — Native mobile strategy

- **Status:** DEFERRED
- **Options:** responsive/PWA first, cross-platform native, or platform-native.
- **Constraint:** audio queues/offline features may eventually justify deeper native support; API/document/audio identity must not depend on browser-only state.

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
- **Phase 1A mitigation:** source contracts explicitly license-gate Semantic Scholar and CORE production workflows where current terms require clearance.

## OD-016 — Formal accessibility target

- **Status:** OPEN
- **Decision window:** before production reader UI freeze.
- **Likely baseline:** current WCAG AA target at implementation time, verified from official standard before ADR.

## OD-017 — Canonical internal ID format

- **Status:** DECIDED
- **Decision:** UUIDv7 for durable NOESYN entity IDs.
- **Authority:** `docs/adr/ADR-0003-postgresql-uuidv7-provenance-core.md`
- **Rule:** explicit event timestamps remain authoritative; UUID ordering is not semantic data.

## OD-018 — Event/provenance persistence model

- **Status:** DECIDED
- **Decision:** append-oriented relational source observations/dependency lineage plus materialized current canonical state; not full event sourcing.
- **Authority:** `docs/adr/ADR-0003-postgresql-uuidv7-provenance-core.md`

## OD-019 — Full-text retention default

- **Status:** OPEN BY DESIGN
- **Rule:** there will be no universal default that overrides rights. Retention is exact content/version/action/license specific.
- **Technical support:** S3-compatible storage must support reusable, limited, entitlement-scoped, transient-only and metadata-only classes.
- **Authority for storage boundary:** `docs/adr/ADR-0006-s3-compatible-artifact-storage-boundary.md`

## OD-020 — Evaluation corpus licensing

- **Status:** OPEN
- **Need:** permanent legal-to-store fixtures spanning JATS/PDF/layout/math/table/figure/version/integrity cases.
- **Blocking:** must be resolved before permanent parser/AI benchmark corpus is committed/stored.

## OD-021 — Production hosting/cloud platform

- **Status:** DEFERRED
- **Constraint:** deployment choice must support PostgreSQL, Temporal workers, private S3-compatible objects, secret management and independent web/API/worker scaling without altering domain identity.

## OD-022 — Authentication/identity provider

- **Status:** DEFERRED
- **Decision window:** before persistent user-library/account functionality.
- **Constraint:** provider identity is an authentication alias; NOESYN user identity must remain portable.

## OD-023 — Observability vendor

- **Status:** DEFERRED
- **Decision window:** before production launch.
- **Required semantics:** structured logs, metrics, traces, source-job/Temporal correlation, privacy-aware redaction and alerting.

## Closing rule

When an open decision is resolved:

1. create an ADR if the choice is durable/architectural;
2. update this register with `DECIDED` and the ADR reference;
3. update affected architecture docs;
4. append the engineering log;
5. add tests/fixtures proving critical assumptions where applicable.
