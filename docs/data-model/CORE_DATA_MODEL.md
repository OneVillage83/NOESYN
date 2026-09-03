# NOESYN Core Data Model

## Purpose

Define semantic entities that must remain stable regardless of implementation language, database technology, or external provider schema.

This is a conceptual model, not final SQL DDL.

## Identity hierarchy

The model separates four commonly conflated concepts:

```text
ScholarlyWork
  └─ WorkVersion
       └─ DocumentArtifact
            └─ StructuredDocument
                 └─ EvidenceUnit
```

### ScholarlyWork

The intellectual work as a canonical NOESYN concept.

Examples:

- a research article considered across its preprint and published forms;
- a systematic review;
- a conference paper;
- a dataset or other research object if later brought into scope.

Candidate fields:

- `work_id` — immutable NOESYN identifier;
- `work_type`;
- `canonical_title`;
- `created_at`;
- `updated_at`;
- `identity_confidence`;
- `identity_status`.

A work is not identified solely by DOI because one work can have multiple identifiers and some works have none.

### ExternalIdentifier

An observed identifier attached to a work or version.

Candidate fields:

- `external_identifier_id`;
- `namespace` — DOI, PMID, PMCID, ARXIV, OPENALEX, S2, ISBN, ISSN-related context, provider-specific ID, etc.;
- `value_normalized`;
- `value_observed`;
- `applies_to_type` — work/version/artifact;
- `applies_to_id`;
- `source_observation_id`;
- `valid_from`;
- `valid_to`;
- `confidence`.

Unique constraints should be namespace-aware, not global string equality.

### WorkVersion

A materially identifiable version/manifestation of a scholarly work.

Candidate `version_type` values:

- PREPRINT
- SUBMITTED_MANUSCRIPT
- ACCEPTED_MANUSCRIPT
- VERSION_OF_RECORD
- UPDATED_VERSION
- CORRECTION
- ERRATUM
- EXPRESSION_OF_CONCERN
- RETRACTION_NOTICE
- WITHDRAWN_VERSION
- UNKNOWN

Candidate fields:

- `version_id`;
- `work_id`;
- `version_type`;
- `version_label`;
- `publication_date`;
- `first_observed_at`;
- `supersedes_version_id` if known;
- `version_confidence`;
- `status`.

Version relationships may be a graph rather than a strict chain.

### SourceRecordObservation

Immutable or append-oriented record of what an external source reported at a point in time.

Candidate fields:

- `source_observation_id`;
- `source_id`;
- `source_record_key`;
- `observed_at`;
- `source_updated_at` if supplied;
- `payload_hash`;
- `normalized_metadata`;
- `raw_payload_ref` where policy permits;
- `adapter_version`;
- `ingestion_run_id`.

This allows metadata changes to be audited rather than silently overwriting history.

### Person

Canonical researcher/person identity.

Candidate fields:

- `person_id`;
- `display_name`;
- `name_variants`;
- `orcid` aliases through ExternalIdentifier;
- `identity_confidence`.

Authorship order belongs on an association entity, not Person.

### Authorship

Association between work/version and person.

Candidate fields:

- `authorship_id`;
- `work_id` or `version_id`;
- `person_id`;
- `author_position`;
- `corresponding_author`;
- `raw_author_name`;
- `affiliations_at_publication[]`;
- `source_observation_id`.

### Organization

Institutions, publishers, funders, repositories, and other organizations may ultimately share a generalized organization identity with typed roles.

Candidate identifiers include ROR and provider-specific identifiers.

### Venue

Journal, conference, repository collection, or other publication venue. ISSN/eISSN are aliases rather than sufficient standalone identity.

## Access and rights entities

### AccessLocation

A known place or mechanism through which a version/artifact may be accessed.

Candidate fields:

- `access_location_id`;
- `version_id`;
- `source_id`;
- `locator`;
- `access_type`;
- `host_type`;
- `format_candidates`;
- `observed_license_id`;
- `requires_authentication`;
- `requires_user_entitlement`;
- `first_observed_at`;
- `last_checked_at`;
- `availability_status`.

### RightsAssertion

A claim about permitted use supported by evidence.

Candidate fields:

- `rights_assertion_id`;
- `subject_type` and `subject_id`;
- `action`;
- `decision` — ALLOW / DENY / UNKNOWN / CONDITIONAL;
- `license_id` if applicable;
- `basis_type` — LICENSE / SOURCE_TERMS / USER_ENTITLEMENT / OWNER_PERMISSION / POLICY / OTHER;
- `evidence_ref`;
- `jurisdiction` if relevant;
- `valid_from` / `valid_to`;
- `confidence`;
- `evaluated_at`;
- `policy_version`.

### License

Normalized license metadata without assuming all source labels are reliable.

Candidate fields:

- `license_id`;
- `spdx_id` when applicable;
- `canonical_name`;
- `canonical_url`;
- `raw_observed_value`;
- `source_id`;
- `commercial_use_flag`;
- `derivatives_flag`;
- `redistribution_flag`;
- `machine_processing_flag`;
- `normalization_confidence`.

## Artifact and document entities

### DocumentArtifact

The exact retrieved bytes or source package.

Candidate fields:

- `artifact_id`;
- `version_id`;
- `access_location_id`;
- `media_type`;
- `format`;
- `content_hash_sha256`;
- `byte_size`;
- `retrieved_at`;
- `acquisition_method`;
- `storage_ref`;
- `storage_policy`;
- `rights_snapshot_id`;
- `retention_class`.

Same-version artifacts from different sources are not assumed byte-identical.

### StructuredDocument

A normalized representation derived from a DocumentArtifact.

Candidate fields:

- `structured_document_id`;
- `artifact_id`;
- `parser_name`;
- `parser_version`;
- `schema_version`;
- `created_at`;
- `parse_quality`;
- `warnings`.

### DocumentNode

Hierarchical semantic node.

Candidate node types:

- DOCUMENT
- TITLE
- ABSTRACT
- SECTION
- HEADING
- PARAGRAPH
- LIST
- LIST_ITEM
- EQUATION
- TABLE
- TABLE_CELL
- FIGURE
- CAPTION
- FOOTNOTE
- REFERENCE
- SUPPLEMENT

Candidate fields:

- `node_id`;
- `structured_document_id`;
- `parent_node_id`;
- `node_type`;
- `ordinal`;
- `content` or structured payload;
- `source_anchor`;
- `source_span`;
- `semantic_metadata`.

### EvidenceUnit

Stable addressable unit used for grounding.

Evidence should usually be finer-grained than a paper-level citation but may aggregate multiple nodes when required for semantic completeness.

Candidate fields:

- `evidence_unit_id`;
- `structured_document_id`;
- `node_refs[]`;
- `normalized_content`;
- `evidence_type`;
- `content_hash`;
- `created_at`;
- `extractor_version`.

## Citation model

### CitationEdge

A source-observed or inferred relationship from one scholarly entity to another.

Candidate fields:

- `citation_edge_id`;
- `citing_work_or_version_id`;
- `cited_work_or_version_id`;
- `reference_node_id`;
- `relationship_type`;
- `source_type` — DECLARED / PROVIDER_INFERRED / NOESYN_INFERRED;
- `source_observation_id`;
- `confidence`.

Citation existence and semantic relationship are separate. A citation does not automatically mean support.

## Claim/evidence model

### Claim

A normalized proposition associated with a paper or synthesis.

Candidate fields:

- `claim_id`;
- `claim_text`;
- `claim_type`;
- `subject_scope`;
- `population_scope`;
- `created_by_type` — AUTHOR_EXTRACTED / NOESYN_MODEL / HUMAN;
- `model_or_extractor_version`;
- `created_at`.

### ClaimEvidenceLink

Candidate fields:

- `claim_id`;
- `evidence_unit_id`;
- `relationship` — SUPPORTS / CONTRADICTS / QUALIFIES / CONTEXT / METHOD / LIMITATION;
- `confidence`;
- `extraction_method`;
- `review_status`.

### DerivedArtifact

Generic parent concept for generated summaries, answers, comparisons, synthesis reports, educational explanations, and audio scripts.

Candidate fields:

- `derived_artifact_id`;
- `artifact_type`;
- `generator_type`;
- `generator_version`;
- `prompt_or_recipe_version`;
- `created_at`;
- `rights_snapshot_id`;
- `quality_state`;
- `content_hash`.

### DerivedArtifactDependency

Allows a generated artifact to declare exact dependencies on claims, evidence units, documents, other derived artifacts, or user context.

## Retraction and integrity model

Retraction/correction state must not be represented as an unversioned boolean on Work.

Use event/relationship entities recording:

- notice identity;
- affected version/work;
- notice type;
- effective/publication dates;
- source;
- reason text where legally/licensably usable;
- current status;
- superseding notices.

## User-domain entities

Public corpus and private user data must be separable.

Candidate entities:

- User
- LibraryItem
- Collection
- Annotation
- Highlight
- ReadingState
- ListeningState
- AudioQueue
- Flashcard
- UserKnowledgeConcept
- UserProvidedArtifact
- UserEntitlement

A user entitlement must never globally alter the public rights status of a work.

## Temporal semantics

Important entities should distinguish:

- when the external fact was effective;
- when a source reported it;
- when NOESYN observed it;
- when NOESYN transformed it.

This distinction is critical for corrections, retractions, licenses, access availability, and reproducible AI output.

## Data-model invariants

1. External provider IDs are not NOESYN primary keys.
2. Work identity and version identity are distinct.
3. Retrieved bytes are content-addressable and tied to provenance.
4. Rights decisions are versioned/evidenced.
5. Derived artifacts declare dependencies.
6. Model-inferred relationships are labeled as inferred.
7. Retractions/corrections cannot be silently lost through metadata overwrite.
8. User entitlements are scoped to the authorized user/context.
9. Source observations retain enough history for audit/replay.
10. Schema evolution is explicit and versioned.
