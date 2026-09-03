# NOESYN Target Architecture

## Status

Phase 0 target architecture. This document defines durable domain boundaries before implementation-stack selection.

## System context

```text
                         ┌─────────────────────────┐
                         │        NOESYN UI        │
                         │ web / mobile / extension│
                         └────────────┬────────────┘
                                      │
                              API / BFF boundary
                                      │
┌─────────────────────────────────────┼─────────────────────────────────────┐
│                                     │                                     │
▼                                     ▼                                     ▼
Discovery & Identity             Research Experience                    User Domain
│                               │                                      │
├─ source search adapters       ├─ reader                              ├─ accounts
├─ canonical work resolver      ├─ paper Q&A                           ├─ library
├─ identifier resolver          ├─ comparison                          ├─ notes
├─ version graph                ├─ synthesis                           ├─ highlights
└─ deduplication                └─ audio                               └─ learning state
│                                     │                                     │
└──────────────────────┬──────────────┴──────────────────────┬──────────────┘
                       │                                     │
                       ▼                                     ▼
                 Access & Rights                       AI / Evidence
                 │                                     │
                 ├─ access resolver                    ├─ evidence extraction
                 ├─ rights engine                      ├─ claim grounding
                 ├─ source policies                    ├─ summarization
                 ├─ acquisition                        ├─ comparison
                 └─ entitlement checks                 └─ synthesis
                       │                                     │
                       └──────────────┬──────────────────────┘
                                      ▼
                             Document Platform
                             │
                             ├─ artifact registry
                             ├─ parsing / normalization
                             ├─ structured document model
                             ├─ figures / tables / math
                             ├─ citations / references
                             └─ provenance ledger
                                      │
                                      ▼
                             Knowledge Graph
                             │
                             ├─ works / versions
                             ├─ authors / institutions
                             ├─ citations
                             ├─ claims / evidence
                             ├─ relationships
                             └─ corrections / retractions
```

## Primary bounded contexts

### 1. Discovery

Responsibilities:

- federated and local search;
- query normalization;
- provider fan-out and ranking;
- semantic retrieval;
- author, venue, institution, concept, and citation exploration;
- candidate generation for synthesis.

Discovery returns candidates, not canonical truth.

### 2. Identity

Responsibilities:

- canonical internal work identity;
- external identifier aliases;
- DOI / PMID / PMCID / arXiv / provider identifier resolution;
- deduplication;
- work-versus-version separation;
- version lineage;
- corrections, expressions of concern, retractions, and updates.

Core rule: **a work and a document version are different entities.**

### 3. Source Integration

Each external provider is represented by an adapter with an explicit source contract covering:

- supported operations;
- authentication;
- rate and bulk-access constraints;
- freshness expectations;
- identifier semantics;
- metadata fields;
- full-text capabilities;
- rights/licensing signals;
- retry/backoff behavior;
- attribution requirements;
- source-specific data quality caveats.

Initial candidate adapters:

- Crossref
- OpenAlex
- Semantic Scholar
- PubMed / PMC
- Europe PMC
- CORE
- Unpaywall
- arXiv
- DOAJ
- institutional repositories
- publisher endpoints where permitted

### 4. Access Resolution

Given a canonical work/version, determine all known access locations and rank them according to:

1. legal usability for the requested action;
2. version preference;
3. source authority;
4. structured-format quality;
5. persistence/stability;
6. latency/cost.

Access resolution should produce evidence, not merely a URL.

Example output concept:

```text
AccessResolution
├─ requested_work_id
├─ candidate_locations[]
│  ├─ source
│  ├─ version_type
│  ├─ url / retrieval handle
│  ├─ observed_license
│  ├─ rights_assertion
│  ├─ rights_confidence
│  ├─ access_type
│  └─ checked_at
└─ selected_location
```

### 5. Rights Engine

The rights engine decides whether a specific action may be performed on a specific artifact under the available evidence and entitlement context.

Actions must be separable, including:

- link;
- retrieve;
- transiently process;
- persist;
- display;
- cache;
- text mine;
- generate embeddings;
- summarize;
- narrate via TTS;
- create derivative artifacts;
- redistribute;
- commercial use.

Default-deny should apply when an action requires rights that cannot be established.

### 6. Document Acquisition

Acquisition records exactly what was retrieved, when, from where, under which access/rights evidence, and with what integrity hash.

Artifacts may include:

- JATS XML;
- publisher XML;
- HTML;
- PDF;
- LaTeX/source package;
- plain text;
- supplementary files;
- figures;
- tables;
- data/code links.

### 7. Document Platform

Normalizes heterogeneous source documents into a canonical structured representation while retaining source offsets/mappings where possible.

Canonical structures should support:

- front matter;
- abstract;
- hierarchical sections;
- paragraphs;
- lists;
- quotations;
- equations;
- tables;
- figures and captions;
- footnotes;
- references;
- supplementary material;
- source anchors.

### 8. Evidence Platform

Creates evidence-addressable units from structured documents.

Evidence units should be immutable or versioned and carry:

- document-version identity;
- location/anchor;
- normalized text or structured payload;
- source checksum/relationship;
- extraction method/version;
- provenance timestamps.

### 9. AI Research Layer

Capabilities:

- paper-grounded Q&A;
- tiered summaries;
- methods/statistics explanation;
- figure/table explanation;
- claim extraction;
- limitations extraction;
- comparison;
- contradiction/support candidate identification;
- literature synthesis;
- learning explanations.

No AI artifact is authoritative solely because a model generated it.

### 10. Audio Platform

Supports:

- verbatim narration when rights permit;
- guided narration;
- AI-generated summaries;
- section-specific playback;
- pronunciation normalization;
- math/citation/table narration policies;
- bookmarks and playback state;
- provenance linking from spoken segments back to source evidence.

### 11. Knowledge Graph

Represents relationships that are not naturally captured in a document store alone.

Candidate edge types:

- CITES
- CITED_BY
- VERSION_OF
- CORRECTS
- RETRACTS
- EXPRESSION_OF_CONCERN_FOR
- SUPPORTS_CLAIM
- CONTRADICTS_CLAIM
- REPLICATES
- EXTENDS
- USES_METHOD
- USES_DATASET
- AUTHORED_BY
- AFFILIATED_WITH
- FUNDED_BY

Machine-inferred semantic edges must be distinguished from publisher/source-declared relationships.

### 12. User Research Domain

Owns:

- saved works;
- collections/playlists;
- annotations;
- listening queues;
- reading/listening position;
- flashcards;
- user knowledge state;
- user-provided documents and entitlements.

User-private data must remain logically separated from public scholarly-corpus data.

## Storage architecture principles

NOESYN should not force every data type into one persistence engine.

Expected storage classes:

- relational database for canonical entities, rights, provenance metadata, and transactional user state;
- object storage for permitted document/audio artifacts;
- search index for lexical/full-text retrieval;
- vector index for semantic retrieval;
- graph representation for relationship-heavy exploration;
- append-oriented event/audit records for important provenance and policy decisions;
- cache for provider responses and expensive derived results.

Specific technologies remain unfrozen in Phase 0.

## Event model

Important transitions should emit durable domain events, for example:

- `source_record_observed`
- `work_identity_resolved`
- `version_linked`
- `access_location_observed`
- `rights_assertion_changed`
- `document_acquired`
- `document_parsed`
- `evidence_units_created`
- `claim_extracted`
- `retraction_observed`
- `summary_generated`
- `audio_generated`

Events should carry schema version and provenance.

## Data lineage requirement

Every derived artifact must have a reconstructible dependency chain.

Example:

```text
Audio segment
  -> generated explanation v3
  -> synthesis artifact v7
  -> claim set
  -> evidence units
  -> structured document v2
  -> source artifact SHA-256
  -> access record
  -> provider/source observation
```

## Failure-domain requirements

External scholarly sources will be incomplete, inconsistent, slow, rate-limited, and occasionally unavailable. Therefore:

- source failures must degrade independently;
- partial search results must expose incompleteness;
- retries must be source-specific;
- provider responses should be cached according to terms/policy;
- identity resolution must tolerate disagreement;
- no single source should be required for all core discovery operations;
- source metadata changes must not silently rewrite historical provenance.

## Observability requirements

Eventually measure at minimum:

- provider availability and error rate;
- search fan-out latency;
- canonicalization/deduplication confidence;
- access-resolution success rate;
- rights-decision distribution;
- parse success and structural quality;
- grounding coverage of AI statements;
- unsupported-claim rate in evaluation;
- audio-generation failures;
- retraction/correction propagation latency;
- user-facing stale-data indicators.

## Security boundaries

Public scholarly metadata, rights-controlled content, user-uploaded documents, credentials, and private user knowledge are separate security domains. Retrieval authorization must occur before restricted artifacts are returned, and derived artifacts must inherit applicable restrictions.

## Deployment principle

The architecture may begin as a modular monolith with background workers if that accelerates development without violating bounded contexts. Service boundaries should be logical first; physical microservices should be introduced only where scale, ownership, security, reliability, or deployment independence justifies them.

## Phase 0 freeze criteria

The target architecture becomes a Phase 0 freeze candidate when:

- core domain boundaries are documented;
- canonical identity/version semantics are documented;
- rights/action semantics are documented;
- source-adapter contract is documented;
- evidence/provenance lineage is documented;
- AI grounding requirements are documented;
- security/privacy baseline is documented;
- the initial roadmap maps implementation phases to this architecture;
- unresolved decisions are explicitly tracked rather than silently assumed.
