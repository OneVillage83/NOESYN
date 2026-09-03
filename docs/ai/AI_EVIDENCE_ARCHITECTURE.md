# NOESYN AI & Evidence Architecture

## Purpose

Define how NOESYN uses AI without turning scholarly literature into an opaque chatbot answer.

The fundamental unit of trust is not the generated paragraph. It is the relationship between a generated statement and the evidence that supports, qualifies, or contradicts it.

## Core rule

**Every consequential generated claim should carry evidence provenance or an explicit unsupported/interpretive status.**

Paper-level citations alone are insufficient when finer evidence is available.

## Output classes

AI-generated content should identify its class, for example:

- `SOURCE_EXTRACTION` — close extraction of what the source states;
- `AUTHOR_INTERPRETATION` — the paper authors' own interpretation;
- `NOESYN_EXPLANATION` — model-generated explanation of source material;
- `NOESYN_SYNTHESIS` — model-generated integration across multiple sources;
- `NOESYN_INFERENCE` — inference not directly stated by the source;
- `USER_NOTE` — user-authored interpretation;
- `UNKNOWN` — classification unavailable.

The UI should not visually blur these categories.

## Grounding pipeline

```text
User question
    ↓
Intent / scope model
    ↓
Candidate work retrieval
    ↓
Version / integrity filtering
    ↓
Evidence-unit retrieval
    ↓
Evidence reranking
    ↓
Structured reasoning task
    ↓
Generated answer with claim boundaries
    ↓
Claim ↔ evidence alignment
    ↓
Grounding verification
    ↓
Answer + evidence map + uncertainty
```

## Paper-grounded Q&A

For “ask this paper”:

1. bind the conversation to an exact WorkVersion / StructuredDocument;
2. retrieve semantically relevant EvidenceUnits plus structural context;
3. generate atomic answer claims;
4. attach evidence links to each claim;
5. verify whether the evidence actually supports the claim;
6. label explanation/inference that goes beyond the source;
7. expose uncertainty or “not answered by this paper.”

The system should prefer “the paper does not establish that” over filling gaps with general model knowledge unless the user explicitly asks for broader context.

## Literature-level synthesis

For “ask the literature”:

```text
Research question
  ↓
Question decomposition / eligibility plan
  ↓
Broad candidate discovery
  ↓
Canonicalization + deduplication
  ↓
Version/integrity filtering
  ↓
Study-type extraction
  ↓
Evidence extraction
  ↓
Evidence table / claim graph
  ↓
Agreement / disagreement analysis
  ↓
Synthesis
  ↓
Claim-level citations + caveats
```

NOESYN should preserve enough intermediate structure to inspect why a particular study was included, excluded, or weighted differently.

## Study characterization

Where relevant, extract structured features such as:

- study design;
- population;
- sample size;
- intervention/exposure;
- comparator;
- outcomes;
- effect estimates;
- confidence intervals / uncertainty;
- statistical tests;
- preregistration/registration identifiers;
- duration;
- attrition;
- limitations;
- funding/conflicts disclosures;
- replication status signals.

Extraction confidence and provenance are required. These are not automatically equivalent to formal risk-of-bias assessment.

## Claim model

Generated answers should be decomposable into atomic or near-atomic claims.

Example:

```text
GeneratedClaim
├─ claim_id
├─ text
├─ type
├─ scope
├─ evidence_links[]
│  ├─ evidence_unit_id
│  ├─ relationship
│  └─ alignment_score
├─ confidence
├─ uncertainty_reason
├─ generator_version
└─ verification_state
```

## Evidence relationship types

At minimum:

- `DIRECT_SUPPORT`
- `INDIRECT_SUPPORT`
- `CONTRADICTS`
- `QUALIFIES`
- `METHOD_CONTEXT`
- `LIMITATION`
- `BACKGROUND`
- `NO_DIRECT_EVIDENCE`

## Summary modes

A single paper may generate multiple explicitly versioned products:

- 2-minute summary;
- 5-minute summary;
- detailed summary;
- methods-focused summary;
- results-focused summary;
- limitations summary;
- beginner explanation;
- undergraduate explanation;
- graduate explanation;
- domain-expert briefing.

All summaries should preserve a dependency map back to the exact source version and evidence units.

## Figure and table explanation

AI may explain figures/tables using:

- caption;
- surrounding textual callouts;
- structured table values;
- figure image/panels where multimodal processing is used;
- methods/results context.

The system must distinguish author-provided descriptions from NOESYN-generated interpretation.

## Statistics explanation

NOESYN should be able to explain:

- what statistical test was used;
- why it may have been chosen;
- effect size versus significance;
- confidence/credible intervals;
- multiple comparisons;
- correlation versus causation;
- missing-data handling;
- common design limitations.

It must avoid pretending that automated explanation is a validated statistical audit unless an explicit validated analysis module supports that claim.

## Contradiction and support detection

A citation is not itself evidence of support.

Potential semantic relationships between papers/claims include:

- supports;
- contradicts;
- partially supports;
- fails to replicate;
- replicates;
- extends;
- uses same method;
- uses same dataset;
- critiques.

Machine-generated relationship edges require confidence and provenance and should be reviewable.

## Integrity-aware synthesis

Before using a paper in synthesis, the pipeline should inspect available signals for:

- retraction;
- correction;
- expression of concern;
- withdrawal;
- superseded preprint/version;
- duplicate publication;
- known data-integrity warnings when available from authoritative sources.

Retracted content may remain discoverable for scholarly context but must be prominently marked and should not be silently used as ordinary supporting evidence.

## Model/provider abstraction

AI capability must sit behind task-level interfaces rather than vendor-specific calls.

Examples:

- `extract_claims`
- `align_claims_to_evidence`
- `summarize_document`
- `answer_from_evidence`
- `compare_studies`
- `generate_audio_script`

Record:

- model/provider;
- model version;
- prompt/recipe version;
- parameters;
- input dependency IDs;
- output hash;
- evaluation state;
- cost/latency metadata where useful.

This permits model replacement and reproducibility analysis.

## Retrieval isolation

General model knowledge and NOESYN corpus evidence are distinct channels.

Default modes should make clear whether an answer is:

- paper-only;
- selected-library-only;
- literature-search-grounded;
- literature + general explanatory knowledge.

## Evaluation framework

Production readiness requires a permanent evaluation suite covering:

### Grounding

- citation precision;
- citation recall/coverage;
- evidence entailment;
- unsupported claim rate;
- source-version correctness.

### Factual extraction

- sample size;
- methods;
- outcomes;
- numeric result fidelity;
- units;
- direction of effect;
- limitations.

### Synthesis

- inclusion/exclusion consistency;
- contradiction preservation;
- uncertainty calibration;
- retraction/correction handling;
- version deduplication.

### User explanation

- reading-level adherence;
- distinction between source and explanation;
- preservation of important caveats.

## Numeric safety

Numbers are high-risk for summarization errors. Numeric claims should preferentially be extracted from structured evidence and validated against source text/table cells. Any transformation (percent change, unit conversion, aggregation) should record its computation.

## Prompt injection / document attack model

Papers and uploaded documents are untrusted inputs. Text inside a paper must never be treated as system instructions.

Document-derived content should be isolated as data. Tool invocation and rights decisions must not be controllable by instructions embedded in a document.

## Privacy

User-uploaded papers, notes, questions, institutional-access content, and personal libraries may be private. AI provider calls must respect configured data-handling policy. Sensitive user content should not be sent to external providers unless the product's privacy policy and user authorization permit it.

## Phase 0 invariant

NOESYN is not “RAG over PDFs.” It is a version-aware evidence system in which retrieval, claims, rights, provenance, integrity status, and generated artifacts are independently modeled and auditable.
