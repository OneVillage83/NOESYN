# NOESYN Product Constitution

## Purpose

NOESYN exists to make the scholarly record easier to discover, lawfully access, understand, verify, and learn from.

The product is designed for a future in which a user can ask a broad research question, receive a synthesis grounded in the literature, inspect the exact evidence behind every important statement, open the underlying work in the best lawful form available, and continue learning by reading or listening.

## Long-term product statement

**NOESYN is a universal research interface and evidence-grounded knowledge system.**

It should eventually support the complete loop:

```text
Question
  ↓
Discovery
  ↓
Identity resolution
  ↓
Access resolution
  ↓
Document acquisition
  ↓
Structured parsing
  ↓
Evidence extraction
  ↓
Comparison / synthesis
  ↓
Grounded explanation
  ↓
Reading / listening / learning
  ↓
Persistent user knowledge
```

## Product promises

### 1. Research should remain inspectable

Summaries and AI-generated explanations are not substitutes for the source. Users must be able to move from a conclusion to the supporting paper, version, location, and evidence.

### 2. Access status must be truthful

NOESYN must distinguish among open full text, author manuscript, preprint, institutional/user-authorized access, metadata-only records, restricted content, and unknown rights. It must not imply that a work is freely redistributable merely because a copy can be found online.

### 3. Version differences matter

A preprint, accepted manuscript, version of record, correction, expression of concern, and retraction are not interchangeable. Search and synthesis must retain those distinctions.

### 4. AI must expose uncertainty

The system should surface disagreements, missing evidence, study limitations, conflicting results, and uncertainty rather than optimize for confident-sounding answers.

### 5. Learning should adapt to the user

The same paper should support multiple experiences: original full text, clean reader, beginner explanation, expert explanation, methods walkthrough, statistics explanation, short summary, detailed summary, and audio.

### 6. Accessibility is core functionality

Responsive reading, semantic structure, keyboard navigation, screen-reader compatibility, and high-quality audio are part of the primary experience.

## Non-goals

NOESYN is not intended to:

- operate as a Sci-Hub clone or redistribute copyrighted full text without authorization;
- replace professional judgment in medicine, law, engineering, or other high-stakes fields;
- hide source disagreement in order to produce a simpler answer;
- collapse scholarly works and document versions into a single loose record;
- depend permanently on one metadata or full-text provider;
- make AI output impossible to audit;
- treat citation count as synonymous with truth or study quality.

## Architectural doctrine

### Full architecture first, staged implementation second

NOESYN will define long-term domain boundaries and invariants before implementation choices become entrenched. Early releases may expose only a subset of capabilities, but that subset should fit the final architecture.

### Durable identifiers

Internal identifiers must remain stable even when external provider records change. External identifiers are aliases and evidence, not the sole internal primary key.

### Immutable provenance events

Acquisition and transformation events should be append-oriented. When a document, license state, source record, or derived artifact changes, NOESYN should preserve enough history to reconstruct what evidence was used at a given time.

### Policy separated from capability

The platform may technically be capable of storing, rendering, processing, or narrating a document. Whether it is permitted to do so is a rights-policy decision and should be separately represented.

### Source-specific behavior stays at the edge

Provider quirks, rate limits, schemas, identifier conventions, and licensing assumptions belong in adapters and source contracts rather than core product logic.

## Product surfaces envisioned

- Web application
- Mobile application
- Browser extension
- Public/internal API
- Audio player and research queues
- Personal research library
- Citation/evidence graph explorer
- Research assistant
- Literature synthesis workspace

## Definition of success

NOESYN succeeds when users can move from a question to a trustworthy understanding of the relevant literature faster than they could by manually searching, opening, reading, reconciling, and tracking papers themselves, without losing the ability to inspect the evidence behind the answer.
