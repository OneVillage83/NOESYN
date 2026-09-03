# ADR-0002: Mixed Python/TypeScript Application Stack

- **Status:** Accepted
- **Date:** 2026-09-03
- **Decision owners:** NOESYN project
- **Scope:** Primary application/runtime languages and web/API frameworks

## Context

NOESYN needs both a scientific/document-processing backend and an unusually rich browser research reader.

Backend workloads include:

- scholarly source adapters and normalization;
- XML/JATS/PDF parsing;
- identity and rights evaluation;
- evidence extraction;
- statistical/ML/AI evaluation pipelines;
- background document/audio processing.

Frontend workloads include:

- responsive structured-paper reader;
- source/evidence synchronization;
- streaming research AI;
- complex annotation/library state;
- accessible audio playback and queue state.

A one-language rule would optimize organizational simplicity at the expense of either the scientific ecosystem or the reader ecosystem.

## Decision

Use a mixed stack:

### Backend/workers

- Python 3.14 line;
- FastAPI for HTTP/API boundary;
- Pydantic for validated transport/configuration boundaries;
- SQLAlchemy 2.x + Alembic for relational persistence/migrations;
- async-safe clients for source integration.

### Web

- TypeScript;
- React via Next.js App Router;
- generated API client/types from FastAPI's OpenAPI schema.

### Dependency tooling

- `uv` for Python dependency/environment locking;
- `pnpm` for TypeScript/web dependency locking.

The physical deployment can remain a modular monolith/API plus workers. This ADR does **not** mandate microservices.

## Contract boundary

FastAPI's generated OpenAPI schema is the authoritative HTTP transport contract.

The TypeScript client must be generated from that schema. Manually duplicated request/response interfaces are not authoritative and generated DTOs are not substitutes for internal domain models.

## Consequences

### Positive

- Python can use the strongest scholarly/scientific parsing and ML ecosystem directly.
- Next.js/React supports the planned reader/audio application without forcing Python UI constraints.
- OpenAPI gives a testable cross-language contract.
- Python workers and API can share domain/source packages initially.
- The architecture can still split physical services later without changing domain identity.

### Costs

- two language toolchains and lockfiles;
- CI must test cross-language schema/client drift;
- contributors must understand transport DTOs versus domain entities.

## Rejected alternative: TypeScript everywhere

Rejected because it would likely require Python later for document/scientific/ML workloads, producing awkward subprocess/service boundaries around core research functionality.

## Rejected alternative: Python everywhere

Rejected because the long-term reader is a browser-first interactive application for which React/TypeScript is the stronger ecosystem.

## Review triggers

Review if:

- Python scientific/document requirements materially disappear;
- a future client platform requires a different presentation technology;
- generated API-contract maintenance becomes a demonstrated major bottleneck.

Do not reverse merely to achieve a nominal one-language repository.