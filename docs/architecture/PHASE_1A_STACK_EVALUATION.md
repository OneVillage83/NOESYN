# NOESYN Phase 1A Stack Evaluation

- **Status:** COMPLETE — recommendations accepted via Phase 1A ADRs
- **Date:** 2026-09-03
- **Scope:** durable implementation choices needed before Phase 1B schema/scaffold work

## Evaluation rule

Technology is selected against Phase 0 invariants, not familiarity alone.

Highest-weight requirements:

1. Work/WorkVersion/Artifact/Evidence relational integrity;
2. append-oriented source provenance and replay;
3. scientific XML/PDF/NLP ecosystem;
4. high-concurrency scholarly APIs;
5. durable multi-step background workflows;
6. generated/typed API contracts for the reader;
7. responsive, accessible, state-rich web reader/audio UI;
8. ability to add search/vector/graph projections without making them canonical;
9. reproducible dependency/tooling setup;
10. modest early operational complexity without throwaway architecture.

---

## 1. Backend language/framework

### Candidates

#### A. Python-only full stack

Strengths:

- unmatched scientific/NLP/document-processing ecosystem;
- strong XML/PDF/data tooling;
- same language for ingestion, parsers, evaluation and ML/AI workers.

Weaknesses:

- weaker fit than TypeScript/React for the sophisticated browser reader;
- would either constrain UI choices or still introduce JavaScript/TypeScript later.

#### B. TypeScript-only backend + frontend

Strengths:

- one application language;
- excellent web/API ecosystem;
- strong type sharing.

Weaknesses:

- forces scholarly parsing, scientific Python libraries, ML/evaluation pipelines and many future research tools behind subprocess/service boundaries;
- likely causes Python to enter later anyway.

#### C. Mixed Python backend/workers + TypeScript web

Strengths:

- each ecosystem is used where it is strongest;
- Python owns scholarly adapters, identity, rights, acquisition, parsing, evidence, AI/audio workers;
- TypeScript/React owns the rich browser UI;
- OpenAPI provides a language-neutral API contract and generated client boundary;
- physical deployment can remain a modular monolith plus workers rather than immediate microservices.

Cost:

- two language toolchains;
- generated contract discipline is mandatory.

### Decision

**Select mixed Python + TypeScript.**

Initial backend:

- Python 3.14 line;
- FastAPI;
- Pydantic models at API/config boundaries;
- SQLAlchemy 2.x + Alembic;
- PostgreSQL via a supported modern driver;
- async-safe HTTP client for external scholarly APIs.

Initial web:

- TypeScript;
- React via Next.js App Router;
- generated client/types from the backend OpenAPI schema rather than manually duplicating backend domain contracts.

### Why FastAPI

Current official FastAPI documentation confirms automatic OpenAPI/JSON Schema generation and interactive API docs. That creates a strong contract boundary for a mixed-language architecture and makes schema drift testable.

### Why Python 3.14 baseline

Current Python 3.14 includes standard-library RFC 9562 UUIDv7 support, reducing custom ID-generation dependencies. Exact minimum patch versions will be pinned during scaffold creation.

---

## 2. Canonical relational database

### Candidates

- PostgreSQL;
- MySQL/MariaDB;
- distributed SQL from day one;
- document/database-first design.

### Decision

**Select PostgreSQL 18+ as the canonical transactional data store.**

Reasons:

- strong foreign keys, constraints and transactions for identity/provenance;
- native UUID type and UUIDv7 generation in PostgreSQL 18;
- JSONB for raw/normalized source payload supplements without giving up relational truth;
- native full-text search sufficient for initial metadata search;
- mature indexing, partitioning, backup/replication ecosystem;
- supports append-oriented observation tables cleanly;
- avoids forcing canonical entities into vector/graph/search-specific stores.

### Explicit non-decision

PostgreSQL is canonical storage, not a promise that every future search/graph/vector workload must remain inside PostgreSQL.

Search, vector and graph systems remain rebuildable projections.

---

## 3. Internal identifier format

### Candidates

- integer sequences;
- UUIDv4;
- ULID;
- UUIDv7.

### Decision

**Use UUIDv7 for durable NOESYN entity IDs.**

Why:

- standard RFC 9562 identifier;
- opaque/non-sequential public identity;
- time-ordered for better locality than random UUIDv4;
- native support in current Python 3.14 and PostgreSQL 18;
- no proprietary ID format.

Rules:

- time encoded in UUIDv7 is operational metadata, not authoritative `created_at`;
- every table still has explicit timestamps;
- external IDs remain separate typed aliases;
- do not derive user-visible meaning/security from UUID ordering.

---

## 4. Provenance persistence

### Candidates

- destructive current-state rows only;
- full event sourcing;
- append-oriented relational observations + materialized current state;

### Decision

**Select append-oriented relational observations plus materialized/current canonical state.**

This is deliberately not full event sourcing.

Core pattern:

```text
source_observation (append-oriented)
        ↓
identity/canonicalization decision
        ↓
canonical current state
        ↓
derived artifact dependency rows
```

Raw/source payloads are content-addressed or hash-identified where retention permits. Materialized canonical state can change without destroying the historical observations that justified prior results.

---

## 5. Initial metadata source pair

### Candidates

- Crossref only;
- Crossref + OpenAlex;
- Crossref + Semantic Scholar;
- OpenAlex only.

### Decision

**Crossref + OpenAlex.**

Why Crossref:

- DOI-centric publisher/member-deposited metadata;
- strong exact-identifier role;
- official API plus large-scale data-file strategy.

Why OpenAlex:

- broad graph/works/authors/institutions coverage;
- CC0 metadata under current official terms;
- official bulk snapshot and incremental API model;
- citation/access-location enrichment;
- useful non-DOI candidate generation.

Why not Semantic Scholar as the initial bulk pair:

- it remains highly valuable for citations/recommendations/semantic enrichment;
- current API/data licensing needs explicit clearance for the intended public/commercial production use before broad persistent dataset ingestion.

Biomedical enrichment begins with PubMed/PMC/Europe PMC under their separate contracts.

---

## 6. Search corpus strategy

### Candidates

- federated provider APIs for every user search;
- full provider snapshots before any search product;
- hybrid.

### Decision

**Hybrid, local-first canonical results with federated candidate expansion.**

Phase 1 behavior:

1. search locally known canonical/source-observation metadata first;
2. use allowed external source search for candidate expansion/freshness when needed;
3. canonicalize/deduplicate external candidates before presenting them as durable NOESYN works;
4. progressively synchronize official snapshots as corpus scale grows.

NOESYN must not become permanently dependent on one live provider request per user search.

---

## 7. Search engine

### Decision

**Use PostgreSQL full-text/indexed metadata search initially.**

Reason:

- sufficient for Phase 1 identity/discovery workloads;
- keeps canonical + search projection close while corpus is small;
- PostgreSQL has built-in text search, ranking and indexing;
- avoids Elasticsearch/OpenSearch/Meilisearch operational cost before relevance/scale data exists.

A dedicated search engine may be added behind a projection boundary when measured requirements exceed PostgreSQL.

---

## 8. Vector retrieval

### Decision

**Deferred.**

Embedding indexes are rebuildable projections. Do not add pgvector or a dedicated vector database until the Paper-Grounded AI phase has an evaluation workload that requires it.

The canonical evidence store remains relational/source-anchored regardless of vector technology.

---

## 9. Graph storage

### Decision

**Relational edge tables initially; specialized graph database deferred.**

Citation/claim/version/integrity graph semantics are more important than the engine. PostgreSQL can represent typed/provenance-aware edges for early workloads. Add a graph projection later only when traversal workloads justify it.

---

## 10. Background orchestration

### Candidates

- in-process background tasks;
- simple Redis/Celery/RQ-style queues;
- PostgreSQL job table;
- Temporal durable workflows.

### Decision

**Select Temporal as the target durable workflow orchestrator, introduced when Phase 1B begins multi-step sync jobs.**

NOESYN has unusually strong durable-workflow requirements:

- provider rate/backoff windows;
- multi-page source sync;
- acquisition → parse → evidence pipelines;
- retryable external calls;
- long-running AI/audio generation;
- integrity-triggered reprocessing;
- reproducible status/audit trails.

Current Temporal Python documentation explicitly supports durable execution, Activity retry policies, idempotency guidance and event-history replay testing. Those capabilities align with NOESYN's replay/provenance doctrine better than ad-hoc queue retry state.

Constraints/cost:

- Temporal is additional infrastructure;
- Workflow code must obey deterministic/replay-safe rules;
- side effects belong in Activities;
- Activity operations must be idempotent because retries can occur;
- workflow history replay should be part of CI for important workflow changes.

We will not put ordinary request/response API work through Temporal unnecessarily.

---

## 11. Object storage

### Decision

**Standardize the application boundary on S3-compatible object storage; defer production vendor selection.**

Required semantics:

- private by default;
- content hashes/object metadata;
- signed time-limited access;
- lifecycle/retention policies;
- separate prefixes/buckets or policy domains for reusable, restricted, user-private and generated artifacts;
- encryption at rest;
- local-development equivalent.

AWS S3 is a valid production implementation and supports presigned URLs/encryption, but NOESYN domain code must not embed AWS-specific public identity into canonical records.

---

## 12. Frontend

### Decision

**Next.js App Router + TypeScript + React.**

Reasons:

- mature React ecosystem for a complex reader;
- server/client component boundary;
- streaming/dynamic UI support;
- TypeScript;
- explicit accessibility tooling and JSX accessibility linting;
- strong responsive web/PWA path before native mobile is justified.

Native mobile remains deferred, but API/audio/document contracts must not depend on browser-only identity.

---

## 13. Dependency/tooling strategy

### Python

Use **uv** for Python project/environment/dependency locking. Current official documentation provides cross-platform lockfiles and workspace support.

### TypeScript

Use **pnpm** for the web workspace/package manager with its lockfile committed.

### API contract

FastAPI OpenAPI JSON is the authoritative transport contract. Generate TypeScript client/types in CI/build tooling; fail drift checks if generated client is stale.

Do not use generated transport DTOs as the canonical internal domain model on either side.

---

## 14. Initial test stack

Backend/source/domain:

- pytest;
- async tests where applicable;
- deterministic provider fixtures;
- property-based tests for normalization/identity invariants where useful;
- migration tests;
- replay/idempotency tests;
- Temporal Workflow replay tests once workflows exist.

Web:

- component/unit tests;
- Playwright for reader/accessibility/end-to-end flows;
- accessibility lint + automated accessibility checks;
- generated-client drift check.

Security:

- secret scanning;
- dependency/security scanning;
- SSRF acquisition tests;
- malicious XML/archive/parser fixtures.

---

## 15. Recommended Phase 1B repository shape

```text
NOESYN/
├─ apps/
│  ├─ api/                 # FastAPI application
│  └─ web/                 # Next.js application
├─ workers/
│  └─ orchestration/       # Temporal worker/workflows/activities
├─ packages/
│  └─ python/
│     ├─ noesyn-domain/
│     ├─ noesyn-sources/
│     └─ noesyn-document/
├─ generated/
│  └─ ts-api-client/       # generated from OpenAPI; reproducible
├─ db/
│  └─ migrations/          # Alembic ownership/configuration
├─ tests/
│  └─ fixtures/
├─ docs/
└─ infra/
   └─ local/               # PostgreSQL/Temporal/S3-compatible local services
```

Exact package boundaries may be simplified during scaffold as long as bounded-context ownership remains clear.

---

## 16. Decisions explicitly still deferred

- production hosting/cloud provider;
- production S3-compatible object-storage vendor;
- dedicated search engine;
- vector engine;
- specialized graph database;
- AI provider/model;
- TTS provider/model;
- native mobile framework;
- production auth provider;
- observability vendor;
- CDN/edge provider.

These do not block Phase 1B canonical identity/source implementation.

## Verification references

Current official materials reviewed during this evaluation include:

- Python 3.14 UUID docs: https://docs.python.org/3/library/uuid.html
- FastAPI OpenAPI docs: https://fastapi.tiangolo.com/tutorial/first-steps/
- PostgreSQL 18 UUID: https://www.postgresql.org/docs/18/functions-uuid.html
- PostgreSQL 18 full-text search: https://www.postgresql.org/docs/18/textsearch.html
- SQLAlchemy 2.x docs: https://docs.sqlalchemy.org/en/20/
- Temporal Python durable/retry/replay docs: https://docs.temporal.io/develop/python/
- Next.js docs: https://nextjs.org/docs
- uv project/workspace docs: https://docs.astral.sh/uv/

## Result

The selected stack preserves Phase 0 semantics while minimizing premature infrastructure. The canonical core remains PostgreSQL-backed and source/evidence-driven; specialized systems stay replaceable projections.