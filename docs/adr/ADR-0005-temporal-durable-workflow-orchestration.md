# ADR-0005: Temporal for Durable Background Workflow Orchestration

- **Status:** Accepted
- **Date:** 2026-09-03
- **Decision owners:** NOESYN project
- **Scope:** Multi-step background workflow execution

## Context

NOESYN background work is not limited to fire-and-forget jobs. Expected workflows include:

- paginated scholarly source synchronization with provider backoff windows;
- snapshot/incremental reconciliation;
- access resolution;
- artifact acquisition;
- artifact → parse → evidence pipelines;
- integrity-triggered reprocessing;
- AI summary/analysis generation;
- long-running audio rendering;
- retries across transient provider/model/storage failures.

These workflows need explicit retry/idempotency state and must survive process restarts/deployments without losing where they were.

## Decision

Use **Temporal** as NOESYN's target durable workflow orchestrator.

Introduce it beginning with multi-step Phase 1B source synchronization/reconciliation rather than building a temporary queue framework first.

### Workflow/activity rule

- Temporal Workflows coordinate durable deterministic state.
- External API/database/object/file/model side effects occur in Activities or otherwise approved deterministic-safe boundaries.
- Activities are designed idempotently because retries can occur.
- Provider-specific retry/backoff behavior is explicit rather than hidden in generic infinite retry loops.

### Testing rule

Important Workflow definitions require event-history replay tests when changed. Current Temporal Python tooling supports replay of Workflow histories to catch nondeterministic/breaking workflow changes.

## Why Temporal

Current official Temporal documentation provides:

- Durable Execution across crashes/infrastructure failures;
- Activity retry policies/timeouts;
- explicit guidance to make Activities idempotent;
- workflow event histories and deterministic replay testing;
- Python SDK support for async and sync Activity implementations.

These behaviors align directly with NOESYN's Phase 0 provenance/replay doctrine.

## What does not go through Temporal

Do not route ordinary low-latency API request/response logic through Temporal merely for consistency. Use it when work has durable multi-step/retry/resume semantics.

Examples normally outside Temporal:

- simple metadata reads;
- ordinary search requests;
- authenticated user CRUD that completes transactionally in one request;
- cache reads.

## Consequences

### Positive

- no custom job-state/retry framework to replace later;
- long-lived provider and document workflows survive worker restarts;
- retry/idempotency semantics are explicit;
- future AI/audio workflows use the same durable control plane;
- workflow replay complements source/data provenance replay.

### Costs

- additional service/infrastructure;
- deterministic Workflow coding constraints;
- workers need deployment/versioning discipline;
- operational knowledge required.

## Rejected alternative: in-process background tasks

Rejected because they do not provide durable recovery and would become unsafe for multi-step corpus/document workflows.

## Rejected alternative: temporary Celery/RQ/simple Redis queue

These can execute jobs, but NOESYN would still need to build durable multi-stage orchestration/checkpoint/retry/version semantics around them. That would be likely throwaway infrastructure under ADR-0001.

## Review triggers

Review if real implementation shows Temporal operational cost is disproportionate to NOESYN workflow durability needs, or if an alternative can preserve equivalent durable replay/idempotency guarantees without a destructive migration.