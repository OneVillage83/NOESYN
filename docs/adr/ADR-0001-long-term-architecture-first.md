# ADR-0001: Design for the Long-Term Architecture First

- **Status:** Accepted
- **Date:** 2026-09-03
- **Decision owners:** NOESYN project
- **Scope:** Repository-wide architecture and implementation strategy

## Context

NOESYN is intended to evolve from scholarly discovery and reading into a universal research interface supporting identity resolution, lawful access, structured documents, evidence-grounded AI, audio, knowledge graphs, literature synthesis, and personal research workflows.

Several concepts become expensive to retrofit after product data accumulates:

- scholarly work versus version identity;
- provenance;
- rights/action semantics;
- artifact lineage;
- corrections/retractions;
- evidence-level grounding;
- private versus public content boundaries.

A conventional MVP approach that models a “paper” as a URL/PDF plus an AI summary would create incompatible assumptions with the intended platform.

## Decision

NOESYN will define the complete long-term domain architecture and invariants before optimizing for the smallest shippable feature set.

Implementation will proceed through **vertical slices of the target architecture**, not throwaway replacements.

This means:

1. domain boundaries are documented before stack decisions become irreversible;
2. internal canonical IDs are owned by NOESYN;
3. Work and WorkVersion are distinct from the first data model;
4. source observations and provenance are preserved;
5. rights are evaluated per action;
6. the shared StructuredDocument/Evidence pipeline powers reader, search, AI, and audio;
7. AI-derived outputs declare dependencies;
8. user entitlements never mutate global access status;
9. implementation may begin as a modular monolith instead of prematurely decomposed microservices;
10. implementation phases may omit capabilities but must not contradict the target model.

## Consequences

### Positive

- reduced need for destructive schema rewrites;
- consistent provenance across product surfaces;
- easier source/provider replacement;
- safer handling of copyrighted/private documents;
- AI/audio features inherit evidence and rights correctly;
- clearer future scaling boundaries;
- better reproducibility of research outputs.

### Negative / cost

- more architecture work before visible UI features;
- additional entities and metadata early in development;
- some abstractions will initially have only one implementation;
- requires discipline to keep documentation synchronized.

## Rejected alternative: thin MVP paper table

Rejected model:

```text
Paper
├─ doi
├─ title
├─ pdf_url
├─ pdf_text
└─ summary
```

Reason: it conflates work/version/artifact/evidence, cannot correctly model rights or entitlements, loses source observations, and provides poor migration paths for retractions and evidence grounding.

## Rejected alternative: microservices first

Long-term bounded contexts do not require physical microservices immediately. A modular monolith can maintain domain boundaries with much lower operational complexity during early development.

## Review triggers

Review this ADR if:

- the product mission materially changes;
- a domain invariant demonstrably prevents a required product behavior;
- production evidence shows a boundary is fundamentally misplaced.

Do not reverse it merely because a shortcut would make a single feature faster to implement.
