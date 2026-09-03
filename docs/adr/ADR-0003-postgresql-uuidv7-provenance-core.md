# ADR-0003: PostgreSQL Canonical Core, UUIDv7 IDs, and Append-Oriented Provenance

- **Status:** Accepted
- **Date:** 2026-09-03
- **Decision owners:** NOESYN project
- **Scope:** Canonical persistence, durable internal IDs, provenance persistence

## Context

NOESYN must maintain strong relational invariants across Work, WorkVersion, source observations, identifiers, access locations, rights assertions, artifacts, structured documents, evidence and derived dependencies.

It also needs source payload flexibility, text search during early phases, and a path to vector/graph/search projections without making specialized stores canonical.

## Decision

### Canonical database

Use **PostgreSQL 18+** as the canonical transactional database.

### Internal IDs

Use **UUIDv7** for durable NOESYN entity identifiers.

- UUIDv7 is opaque public identity, not semantic identity.
- external provider IDs remain typed aliases.
- explicit `created_at`/event timestamps remain authoritative; UUID time is not a substitute.

### Provenance persistence

Use **append-oriented relational observation/dependency tables plus materialized current canonical state**.

This is not full event sourcing.

Typical flow:

```text
SourceRecordObservation (append-oriented)
        ↓
identity/normalization decision
        ↓
canonical current entities
        ↓
DerivedArtifactDependency / RightsDecision / evidence lineage
```

Raw payloads/artifacts use hashes/content-addressed references where retention rights permit.

### Search/vector/graph

- PostgreSQL full-text/indexed metadata search is the initial search projection.
- vector technology remains deferred and rebuildable.
- typed graph edges initially live in relational tables.
- specialized search/vector/graph stores may be added as rebuildable projections when measured workloads justify them.

## Rationale

PostgreSQL provides:

- strong constraints/transactions;
- mature indexes/concurrency/operations;
- native UUID type and current native UUIDv7 generation;
- JSONB for source-specific supplemental structures;
- native full-text search;
- a clean fit for append-oriented observations and dependency tables.

UUIDv7 is standardized under RFC 9562 and is supported by current Python 3.14 and PostgreSQL 18, avoiding a project-specific ID format while preserving useful index locality.

## Consequences

### Positive

- canonical identity stays relational and strongly constrained;
- source disagreement/history is retained;
- specialized stores cannot silently become truth;
- derived artifacts remain auditable/replayable;
- fewer services are needed in early development.

### Costs

- append-oriented observations use more storage than destructive updates;
- canonical materialization/reconciliation logic must be explicit;
- future projections need synchronization/rebuild tooling.

## Rejected alternative: integer IDs

Rejected for durable public identity because sequential IDs expose ordering/count patterns and couple identity unnecessarily to one database sequence.

## Rejected alternative: ULID

ULID provides useful sorting but UUIDv7 now has standardized and native support across the selected Python/PostgreSQL baseline.

## Rejected alternative: full event sourcing

Rejected as unnecessary complexity. NOESYN needs historical source/provenance evidence and replayable transformations, not a requirement to reconstruct every domain entity exclusively by replaying all events.

## Review triggers

Review if PostgreSQL becomes a measured bottleneck for a canonical transactional workload. Do not move canonical identity/evidence to a specialized search/vector/graph engine simply because such an engine is introduced as a projection.