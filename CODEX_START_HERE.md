# NOESYN — CODEX START HERE

## Purpose

This file is the mandatory orientation point for Codex/AI coding agents and human contributors before changing NOESYN.

## Current stage

**Phase 0 — Architecture & Governance**

Production implementation has not begun. Do not introduce a stack or schema casually just to create visible application code.

Read these documents before implementation work:

1. `README.md`
2. `docs/PRODUCT_CONSTITUTION.md`
3. `docs/architecture/TARGET_ARCHITECTURE.md`
4. `docs/data-model/CORE_DATA_MODEL.md`
5. `docs/rights/RIGHTS_AND_ACCESS_MODEL.md`
6. `docs/sources/SOURCE_STRATEGY.md`
7. `docs/document-engine/DOCUMENT_PLATFORM.md`
8. `docs/ai/AI_EVIDENCE_ARCHITECTURE.md`
9. `docs/audio/AUDIO_ARCHITECTURE.md`
10. `docs/knowledge-graph/KNOWLEDGE_GRAPH_ARCHITECTURE.md`
11. `docs/reader/READER_ARCHITECTURE.md`
12. `docs/security/SECURITY_AND_PRIVACY_BASELINE.md`
13. `docs/adr/ADR-0001-long-term-architecture-first.md`
14. `ROADMAP.md`
15. `docs/engineering-log/ENGINEERING_LOG.md`

## Non-negotiable invariants

Do not violate these without a new ADR explicitly superseding the relevant decision.

### Identity

- `ScholarlyWork` and `WorkVersion` are different concepts.
- External IDs such as DOI, PMID, PMCID, arXiv, OpenAlex, and Semantic Scholar IDs are aliases/observations, not NOESYN primary keys.
- Corrections/retractions/version changes must remain representable.

### Provenance

- Important source observations are timestamped/versioned.
- Acquired artifacts have provenance and integrity hashes where retained.
- Derived summaries/answers/audio declare dependencies.
- Historical outputs must not silently change when source metadata or parsers change.

### Rights

- “Open access” is not a universal permission bit.
- Rights are evaluated per action.
- Unknown rights do not default to permission for restricted actions.
- User entitlement never becomes global public access.

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

## Architecture-first implementation rule

Early product functionality must be a vertical slice of the target architecture.

Example of an acceptable early slice:

```text
Crossref source record
→ canonical Work/WorkVersion
→ Unpaywall access candidate
→ rights decision
→ permitted PMC JATS artifact
→ StructuredDocument
→ EvidenceUnit
→ clean reader
→ grounded summary
→ audio summary
```

Only part of each domain may exist initially, but the data lineage fits the target model.

Example of an unacceptable shortcut:

```text
papers table
→ pdf_url
→ extracted_text blob
→ ask LLM
→ summary string
```

if implemented in a way that discards version, rights, provenance, and evidence semantics.

## Documentation rule

After every meaningful change:

1. update the relevant architecture/source/schema/operation documentation;
2. append a timestamped entry to `docs/engineering-log/ENGINEERING_LOG.md`;
3. update `CHANGELOG.md` when the change is user-facing or materially architectural;
4. add/update an ADR when a durable architectural choice is made;
5. update `ROADMAP.md` status when a milestone changes.

The engineering-log entry should capture:

- UTC or timezone-qualified timestamp;
- what changed;
- why;
- files/components touched;
- commands/tests run;
- result;
- unresolved risks/next step;
- commit/PR reference when known.

## Research/source changes

Never implement a scholarly source based on remembered API behavior.

Before coding an adapter:

1. verify current official documentation;
2. create/update a source contract under `docs/sources/contracts/`;
3. record endpoint/version/auth/rate/bulk/terms/caching/attribution details;
4. create fixtures;
5. test failure and rate-limit behavior;
6. timestamp when the contract was last verified.

## Stack decisions

The implementation stack is intentionally unfrozen during Phase 0.

Do not infer that a framework/database is selected merely because another OneVillage project uses it.

Any stack proposal should compare options against:

- canonical relational data complexity;
- background ingestion/processing;
- text/search workload;
- AI/data tooling ecosystem;
- document parsing ecosystem;
- deployment/operations burden;
- portability;
- testing;
- cost;
- contributor velocity.

Durable stack decisions should be recorded as ADRs.

## Testing doctrine

Every subsystem eventually needs deterministic fixtures and replay tests.

Priority test classes:

- identity/deduplication;
- version relationships;
- source adapters;
- rights decisions;
- artifact hashing/acquisition;
- parser structure/source anchors;
- evidence extraction;
- AI grounding/numeric fidelity;
- retraction/correction propagation;
- authorization isolation;
- audio source synchronization.

## When uncertain

Do not silently simplify a high-cost-to-retrofit concept. Record the uncertainty in architecture/open-decision documentation, gather evidence, and make the decision explicitly.

## Immediate next work after Phase 0

Phase 1 should begin with **source-contract and implementation-stack evaluation**, followed by canonical identity persistence and the first metadata adapter vertical slice.
