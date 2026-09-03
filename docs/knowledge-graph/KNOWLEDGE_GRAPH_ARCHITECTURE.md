# NOESYN Knowledge Graph Architecture

## Purpose

Represent relationships across scholarly works, versions, people, institutions, citations, claims, evidence, methods, datasets, integrity events, and generated syntheses without confusing observed relationships with machine inference.

## Core principle

A graph edge is a claim about a relationship. Every consequential edge should therefore have provenance, type, confidence, and origin.

## Node classes

Initial conceptual nodes:

- ScholarlyWork
- WorkVersion
- Person
- Organization
- Venue
- Claim
- EvidenceUnit
- Method
- Dataset
- Concept/Topic
- IntegrityNotice
- DerivedArtifact

Not every node class requires a dedicated graph database representation at first. The graph is a semantic model before it is a storage-technology decision.

## Edge classes

### Bibliographic/identity

- `VERSION_OF`
- `SUPERSEDES`
- `AUTHORED_BY`
- `AFFILIATED_WITH`
- `PUBLISHED_IN`
- `FUNDED_BY`
- `HAS_IDENTIFIER`

### Citation

- `CITES`
- `CITED_BY` as a projection rather than necessarily duplicated storage

### Integrity

- `CORRECTS`
- `RETRACTS`
- `EXPRESSES_CONCERN_FOR`
- `WITHDRAWS`
- `UPDATES`

### Scientific relationship

- `SUPPORTS`
- `CONTRADICTS`
- `QUALIFIES`
- `REPLICATES`
- `FAILS_TO_REPLICATE`
- `EXTENDS`
- `CRITIQUES`
- `USES_METHOD`
- `USES_DATASET`
- `STUDIES_CONCEPT`

### Evidence/derivation

- `SUPPORTED_BY_EVIDENCE`
- `DERIVED_FROM`
- `SUMMARIZES`
- `EXPLAINS`
- `COMPARES`

## Edge provenance

Candidate edge envelope:

```text
GraphEdge
├─ edge_id
├─ source_node
├─ target_node
├─ edge_type
├─ origin_type
│  ├─ SOURCE_DECLARED
│  ├─ PROVIDER_INFERRED
│  ├─ NOESYN_INFERRED
│  └─ HUMAN_CURATED
├─ provenance_ref
├─ confidence
├─ method/model_version
├─ created_at
├─ valid_from/to where needed
└─ review_state
```

## Citation is not support

A paper citing another paper may support it, criticize it, cite it as historical context, or mention it for a method. Therefore `CITES` must never be automatically promoted to `SUPPORTS`.

Semantic citation classification is a separate inference task with its own confidence/provenance.

## Claim graph

Long-term synthesis should operate increasingly over normalized claims rather than only documents.

Example:

```text
Claim A: Intervention X improves outcome Y in population Z
   ↑              ↑                     ↑
Evidence 1     Evidence 2            Evidence 3
   ↑              ↑                     ↑
Paper V1       Paper V2               Paper V3
```

Conflicting claims can be linked without forcing premature resolution.

## Version awareness

Relationships may attach to the Work or a specific WorkVersion.

Examples:

- authorship may differ across versions;
- a retraction targets a published version;
- a correction may supersede only part of a version;
- a preprint and version of record may report different numeric values.

Graph queries must preserve that specificity.

## Integrity propagation

When an authoritative retraction/correction event is observed:

1. attach the notice to affected work/version;
2. identify derived claims/artifacts depending on affected evidence;
3. mark dependent synthesis for re-evaluation;
4. surface warning state in search/reader/audio;
5. avoid deleting scholarly history unless required by policy.

## Topic/concept model

Topics may come from multiple ontologies/providers. NOESYN should maintain source-specific concept aliases and mappings rather than assuming one taxonomy is universal across medicine, physics, mathematics, social science, etc.

## Methods and datasets

Long-term nodes for Methods and Datasets enable questions such as:

- Which studies used this assay/model/instrument?
- Which papers reused the same dataset?
- Are apparently independent results derived from overlapping cohorts?

These relationships are high-value but often extraction/inference-heavy, so provenance and confidence are essential.

## Graph computation candidates

Future graph-derived features may include:

- related-paper exploration;
- citation paths;
- seminal/bridge works;
- replication clusters;
- contradictory evidence clusters;
- author/institution networks;
- method/dataset reuse;
- literature evolution over time.

Graph metrics are discovery aids, not intrinsic truth scores.

## Storage strategy

Phase 0 does not mandate Neo4j or any specific graph engine.

Start by designing stable edge/entity semantics. Relational tables may be sufficient initially. Introduce specialized graph storage when traversal/query workload proves the need.

## AI interaction

AI-generated graph edges must never be indistinguishable from source-declared facts. The UI/API must expose origin and confidence.

## Phase 0 invariant

The knowledge graph records **what is connected, who/what asserted the connection, and how confident NOESYN should be in that relationship**. It must not transform uncertain scientific relationships into unqualified facts simply because they are easy to visualize as edges.
