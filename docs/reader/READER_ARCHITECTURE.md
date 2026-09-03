# NOESYN Reader Architecture

## Purpose

Define a research reading experience that is superior to raw PDF viewing while preserving access to the original artifact and exact evidence.

## Core principle

**The clean reader is a view over the canonical StructuredDocument.**

The reader must not create its own independent extraction/parsing pipeline.

## Primary modes

- `READER` — responsive semantic document view;
- `ORIGINAL` — source PDF/HTML/XML view where permitted;
- `FIGURES` — figure-focused exploration;
- `TABLES` — table-focused exploration;
- `REFERENCES` — resolved bibliography/citation navigation;
- `AI` — paper-grounded questions and explanations;
- `AUDIO` — playback synchronized to source/evidence;
- `NOTES` — highlights, annotations, saved concepts.

## Paper header

Candidate content:

- title;
- authors;
- affiliations;
- venue/date;
- DOI/PMID/PMCID/arXiv and other identifiers;
- version type;
- integrity status;
- open/access status;
- license/usage indicator;
- links to publisher/repository/source versions;
- save/listen/ask actions.

Version and integrity warnings must be prominent, not buried in metadata menus.

## Semantic reading

The reader should present:

- abstract;
- hierarchical sections;
- figures and captions;
- tables;
- equations;
- footnotes;
- references;
- supplements;
- author disclosures/funding where present.

The document should reflow naturally on mobile rather than emulate fixed PDF pages.

## Original synchronization

Where source anchors are available, users should be able to move from a clean-reader passage to the corresponding location in the original artifact and vice versa.

Examples:

- paragraph → PDF page/bounding box;
- figure → original figure location;
- citation → reference entry;
- AI evidence citation → exact reader passage.

## Explain interaction

Selecting text/node content may expose actions such as:

- Explain simply
- Explain at undergraduate level
- Explain at graduate level
- Explain for an expert
- Define terms
- Explain equation
- Explain statistics
- Find prerequisite concept
- Find supporting papers
- Find conflicting papers
- Add to notes
- Create flashcard
- Listen from here

Generated explanations must visibly differ from original source text.

## Evidence indicators

AI summaries/answers should present claim-level evidence links. Activating a link should focus or highlight the underlying EvidenceUnit rather than only opening the paper landing page.

## Citation navigation

Inline citation markers should be interactive.

Possible panel:

```text
[12] Smith et al. 2024
Status: Open manuscript available
Relationship: cited as background (machine-inferred, medium confidence)

[Open] [Listen to summary] [Why cited?] [Citation graph]
```

Semantic relationship labels must disclose when machine-inferred.

## Figures

Figure focus mode should support:

- high-resolution image where permitted;
- caption;
- author-provided alt text where available;
- in-text references to the figure;
- AI explanation clearly labeled as generated;
- panel navigation;
- evidence links to results/methods;
- listen-to-figure explanation.

## Tables

Table mode should preserve semantics and support:

- sticky headers;
- accessible row/column navigation;
- sort/filter only when it does not alter scholarly meaning without disclosure;
- value highlighting;
- unit display;
- source footnotes;
- AI summary with exact cell/evidence references.

## Equations

Equation rendering should support accessible MathML/semantic alternatives where possible and provide:

- rendered notation;
- copy as LaTeX where source/rights allow;
- spoken form;
- symbol definitions;
- step-by-step explanation as generated content.

## Annotation model

Highlights and notes should anchor to stable canonical nodes/evidence units rather than fragile screen offsets.

When a document is reparsed or a new version appears, the system should attempt explicit annotation migration and surface unresolved anchors rather than silently moving notes to the wrong text.

## Version comparison

Long-term reader capability:

- preprint versus version of record;
- revision-to-revision differences;
- corrected versus prior text;
- changed figures/tables/results;
- added/removed authors;
- changed conclusions.

Diffs must operate on versions, not assume all copies are identical.

## Accessibility baseline

The reader must target:

- semantic headings;
- keyboard navigation;
- visible focus;
- screen-reader labels;
- logical reading order;
- adjustable typography;
- sufficient contrast;
- captions/transcripts;
- accessible math/table navigation;
- reduced-motion support;
- no feature that requires hover alone.

Formal accessibility target/version should be frozen before public UI implementation.

## Offline behavior

Offline reading/listening may be supported only for artifacts/derivatives whose rights and user entitlement permit local storage. Offline capability must not bypass access controls.

## Performance

Large papers must not require rendering the entire node tree at once. Consider section virtualization/lazy loading while preserving stable anchors and search/navigation.

## Reader state

Persist separately:

- reading position;
- active version;
- selected mode;
- highlights/notes;
- audio position;
- expanded/collapsed sections;
- user preferences.

## Integrity warnings

Retracted, withdrawn, corrected, or expression-of-concern states should follow users across:

- search results;
- paper header;
- AI answers;
- audio intros;
- saved library items.

## Phase 0 invariant

NOESYN should never force the user to choose between **convenient understanding** and **inspection of the original evidence**. The reader is designed to make both available from the same provenance-aware document model.
