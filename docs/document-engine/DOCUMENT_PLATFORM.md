# NOESYN Document Platform

## Purpose

Convert heterogeneous scholarly artifacts into a versioned, provenance-preserving structured representation that can power reading, search, AI grounding, audio, figures/tables, citation parsing, accessibility, and future exports.

The canonical structured document is a core platform primitive. The reader, AI layer, and audio layer must not independently parse the same paper into incompatible forms.

## Inputs

Potential input formats include:

- JATS XML;
- publisher XML;
- HTML;
- PDF with embedded text;
- scanned/image PDF as a lower-confidence fallback;
- LaTeX/source packages;
- plain text;
- supplementary files;
- standalone figures/tables.

Format support must be capability-driven and versioned.

## Pipeline

```text
AccessLocation
      ↓
Rights check: RETRIEVE
      ↓
DocumentArtifact
      ↓
Integrity / media inspection
      ↓
Format-specific parser
      ↓
Canonical node tree
      ↓
Normalization / reference linking
      ↓
Quality assessment
      ↓
StructuredDocument
      ↓
Evidence segmentation
      ↓
EvidenceUnits
```

## Artifact preservation

When permitted, preserve the exact acquired bytes and record:

- cryptographic content hash;
- byte size;
- media type;
- source/access location;
- retrieval timestamp;
- retrieval method;
- applicable rights snapshot;
- source-provided ETag/Last-Modified where useful;
- acquisition software/version.

If retention is not permitted, retain only policy-permitted metadata/hash/provenance sufficient to explain the transient processing event.

## Canonical node tree

A structured document should be an ordered hierarchy rather than one flat text blob.

Baseline nodes:

```text
Document
├─ FrontMatter
│  ├─ Title
│  ├─ Authors
│  ├─ Affiliations
│  ├─ Abstract
│  └─ Metadata
├─ Body
│  ├─ Section
│  │  ├─ Heading
│  │  ├─ Paragraph
│  │  ├─ Equation
│  │  ├─ Figure
│  │  └─ Table
│  └─ ...
├─ BackMatter
│  ├─ Acknowledgements
│  ├─ Funding
│  ├─ Conflicts
│  ├─ DataAvailability
│  └─ References
└─ Supplements
```

## Source anchors

Every normalized node should retain a best-effort mapping to the source representation.

Depending on source type, anchors may include:

- XML element path/ID;
- HTML DOM selector/ID;
- PDF page and bounding box;
- character offsets;
- LaTeX source file/line mapping;
- figure/table identifiers.

Source anchors allow a derived claim to jump back to the original evidence rather than only to a paper landing page.

## PDF policy

PDF is an important source format but not the ideal canonical representation.

PDF ingestion should separate:

1. text extraction;
2. layout analysis;
3. reading-order reconstruction;
4. heading/section classification;
5. table/figure detection;
6. reference parsing;
7. equation handling;
8. confidence scoring.

Do not silently present low-confidence PDF reconstruction as publisher-authored structure.

## Structured XML preference

When the same version is legally available both as machine-readable structured XML and PDF, prefer structured XML for semantic extraction while retaining the PDF as a presentation/source artifact when allowed.

JATS-rich sources such as permitted PMC content should preserve native semantic structure wherever possible.

## Figures

Figure representation should include:

- figure ID;
- caption;
- label/number;
- image artifact reference;
- source anchor;
- in-text callouts;
- alternative text when provided;
- generated accessibility description kept separate from author-provided text;
- panel segmentation where reliable.

AI-generated figure explanations must identify themselves as derived content.

## Tables

Preserve tables structurally rather than flattening them into prose where possible.

Table representation should support:

- caption;
- headers;
- rows/cells;
- spanning cells;
- footnotes;
- units;
- source anchor;
- semantic labels.

The audio layer may generate a table summary rather than reading every cell unless the user requests verbatim table narration and rights permit it.

## Mathematics

Store equations as structured math when obtainable, with source rendering retained when necessary.

Candidate representations:

- MathML;
- LaTeX;
- normalized symbolic form;
- source image fallback.

Audio narration should be generated from math structure, not naive character-by-character TTS.

## References

Each reference node should preserve:

- raw citation string;
- parsed fields;
- author/title/year/venue;
- identifiers discovered;
- resolved NOESYN work/version if available;
- confidence;
- source anchor.

Reference resolution is appendable and may improve later without rewriting the original reference text.

## Evidence segmentation

Evidence units should be optimized for faithful grounding, not merely fixed token windows.

Segmentation may consider:

- paragraph boundaries;
- section hierarchy;
- table rows/regions;
- figure captions/panels;
- methodological blocks;
- statistical result statements;
- nearby definitions/context.

Each evidence unit must point back to one or more canonical nodes.

## Quality model

Parsing quality should be explicit.

Candidate dimensions:

- text completeness;
- reading order;
- section detection;
- reference resolution;
- table fidelity;
- equation fidelity;
- figure mapping;
- source-anchor quality.

The system should be able to say “this PDF parse is low confidence” and fall back to the original source view.

## Parser versioning

A StructuredDocument is created by a specific parser pipeline version. Reparsing an artifact with a better parser creates a new structured-document version rather than silently replacing historical structure used by prior AI outputs.

## Content mutation rules

Normalization may fix representation issues such as whitespace and encoding but must not silently alter scholarly meaning.

Corrections such as OCR fixes should preserve:

- original extracted form;
- corrected form;
- correction method;
- confidence;
- provenance.

## Search projections

The document platform should publish projections for:

- lexical full-text search;
- semantic/vector retrieval;
- section-filtered retrieval;
- figure/table search;
- reference search.

Indexes are rebuildable projections, not canonical source of truth.

## Reader projections

The reader should consume canonical structured nodes plus display policy, allowing:

- responsive typography;
- collapsible sections;
- inline citations;
- equation rendering;
- figure/table focus mode;
- source/PDF synchronization;
- highlights/annotations against stable anchors.

## Audio projections

The audio system consumes structured semantic nodes and generates a narration plan. It must not parse raw PDFs independently.

## AI projections

The AI layer consumes evidence units and structured nodes. It must retain evidence IDs and document/version provenance in generated outputs.

## Testing requirements

Build a permanent fixture corpus representing:

- clean JATS;
- complex JATS;
- clean born-digital PDF;
- multi-column PDF;
- equation-heavy PDF;
- table-heavy PDF;
- figure-heavy PDF;
- malformed PDF;
- scanned PDF;
- preprint source;
- corrected/retracted article;
- version pairs.

Fixtures must use content we are allowed to store in the repository/test environment.

## Phase 0 invariant

**There is one canonical document/evidence pipeline.** Reader, AI, audio, search, and graph features consume its versioned outputs instead of creating private competing representations of a paper.
