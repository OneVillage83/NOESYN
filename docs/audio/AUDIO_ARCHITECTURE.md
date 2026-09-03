# NOESYN Audio Architecture

## Purpose

Turn scholarly research into a trustworthy listening experience without creating an audio subsystem that loses document structure, provenance, or rights constraints.

The long-term target is an **Audible-style research experience** with full-paper narration where permitted, guided narration, summaries, section playback, research queues, bookmarks, and evidence-linked spoken explanations.

## Core rule

**Audio is a projection of the canonical structured document and evidence system.**

The audio subsystem must not independently parse raw PDFs or create untraceable scripts.

## Audio modes

### 1. Full-paper narration

A faithful spoken rendering of permitted source text with intelligent treatment of:

- headings;
- citations;
- abbreviations;
- equations;
- tables;
- figures;
- footnotes;
- references;
- pronunciation.

Whether full source text may be converted to TTS must be decided by the rights engine for the exact artifact/version.

### 2. Guided paper

A generated educational walkthrough preserving paper structure while explaining what each section is doing.

Typical sequence:

```text
Context
→ Research question
→ Methods
→ Key results
→ Figures/tables
→ Authors' interpretation
→ Limitations
→ What this does and does not establish
```

The script is a derived artifact and must keep evidence links.

### 3. Summary audio

Possible durations:

- ~2 minutes;
- ~5 minutes;
- ~10 minutes;
- detailed/long-form;
- user-selected duration.

Duration is a target, not permission to omit material caveats that materially change the meaning.

### 4. Section playback

Users should be able to listen specifically to:

- abstract;
- introduction/background;
- methods;
- results;
- discussion;
- limitations;
- selected figure/table explanation;
- references if desired.

### 5. Literature briefing

Long-term mode that produces an evidence-grounded lesson or briefing across many papers for a requested topic and duration.

Example:

> “I have an hour drive. Teach me what the literature says about X.”

The briefing should expose its paper/evidence playlist when the user returns to the visual interface.

## Pipeline

```text
User audio request
      ↓
Resolve work/version or synthesis scope
      ↓
Rights check for requested audio operation
      ↓
StructuredDocument / Evidence dependencies
      ↓
Narration planner
      ↓
Script / speech-token representation
      ↓
Grounding + policy validation
      ↓
TTS rendering
      ↓
Audio segments
      ↓
Manifest + evidence/source anchors
```

## Narration plan

Before TTS, generate a structured narration plan rather than a single giant text string.

Candidate structure:

```text
NarrationPlan
├─ mode
├─ work/version/synthesis IDs
├─ voice/settings
├─ segments[]
│  ├─ segment_id
│  ├─ segment_type
│  ├─ source_node_refs[]
│  ├─ evidence_refs[]
│  ├─ script_text
│  ├─ pronunciation hints[]
│  ├─ rights_basis
│  └─ expected_duration
└─ generation metadata
```

This allows seekable playback, source synchronization, regeneration, and evidence inspection.

## Source versus generated speech

The player should distinguish at least:

- verbatim source narration;
- paraphrased source narration;
- NOESYN explanation;
- literature synthesis;
- warning/integrity notice.

For example, a guided paper should not make generated explanatory prose sound as if it were a direct quotation from the authors.

## Citations in speech

Naive citation reading is disruptive. Policies should be mode-specific.

Possible behavior:

- full/verbatim mode: preserve citation presence while speaking a concise form;
- guided mode: omit routine inline citation numbers from speech but retain them in segment metadata;
- citation-focused mode: speak authors/year or reference information;
- user action: “show sources for what I just heard.”

The source trail remains available even when citation markers are not spoken aloud.

## Equations

Do not feed raw equation markup directly into generic TTS.

Equation narration should use:

1. structured math representation;
2. a math-to-speech renderer;
3. context-aware verbosity setting.

Modes may include:

- concise conceptual reading;
- literal equation reading;
- step-by-step explanation.

Generated equation explanation is separate from verbatim source narration.

## Tables

Default guided narration should summarize the table's purpose and important values with evidence anchors rather than reading every cell.

Users may request:

- overview;
- row/column exploration;
- full accessible table reading.

Numeric fidelity checks are required for generated table summaries.

## Figures

Figure narration can combine:

- author caption;
- in-text discussion;
- structured values if available;
- multimodal NOESYN interpretation.

AI visual interpretation must be labeled as generated and should state uncertainty when a panel cannot be reliably read.

## Pronunciation layer

Maintain a pronunciation lexicon for:

- author names;
- scientific terms;
- gene/protein symbols;
- chemical names;
- mathematical notation;
- abbreviations;
- units.

Allow user corrections and domain-specific dictionaries. Pronunciation rules should be versioned.

## Audio artifact model

Audio should be segmented and content-addressed where useful.

Candidate artifacts:

- source narration script;
- guided script;
- SSML/speech markup;
- individual audio segment;
- final manifest;
- waveform/timing metadata;
- transcript.

A manifest maps timestamps/segments to source/evidence IDs.

## Rights inheritance

Audio files and scripts inherit rights constraints from their dependencies plus the rights decision under which they were created.

If a source is entitlement-scoped, generated audio must not automatically become globally shareable.

## Caching and regeneration

Cache audio only when policy permits. Cache identity should include:

- source/derived content hash;
- narration recipe version;
- TTS engine/model version;
- voice;
- speed/options;
- pronunciation dictionary version.

A changed paper version or corrected script must not silently reuse stale audio.

## Playback experience

Long-term player features:

- variable speed;
- sleep timer;
- bookmarks;
- section navigation;
- continue across devices;
- transcript sync;
- highlight currently spoken evidence;
- “show source” action;
- “explain this” action;
- research queue/playlists;
- downloaded/offline playback where rights permit.

## Accessibility

Audio is both a convenience and an accessibility feature. The transcript, controls, semantic sections, keyboard behavior, screen-reader labels, and non-audio alternatives must be designed together.

## Evaluation

Measure:

- source-text fidelity;
- numeric fidelity;
- pronunciation error rate;
- section-order fidelity;
- hallucinated explanation rate;
- evidence-link coverage;
- timing/source synchronization;
- user comprehension;
- listening completion/seek behavior without using engagement as a substitute for accuracy.

## Phase 0 invariant

NOESYN audio must always be able to answer two questions:

1. **What am I hearing—source text, author interpretation, or NOESYN-generated explanation?**
2. **What paper/version/evidence does this spoken segment come from?**
