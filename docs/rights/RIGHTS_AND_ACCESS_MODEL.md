# NOESYN Rights & Access Model

## Purpose

NOESYN must be capable of discovering broadly while performing full-text actions only when those actions are supported by rights, source terms, or user authorization.

Rights are modeled as explicit data and policy decisions rather than inferred from whether a URL happens to be reachable.

## Core distinction

These are separate questions:

1. **Can NOESYN find a record for this work?**
2. **Can NOESYN link the user to an external copy?**
3. **Can NOESYN retrieve the full text?**
4. **Can NOESYN persist it?**
5. **Can NOESYN display it?**
6. **Can NOESYN text-mine or embed it?**
7. **Can NOESYN summarize it?**
8. **Can NOESYN generate TTS/audio from it?**
9. **Can NOESYN redistribute the original or a derivative?**
10. **Can NOESYN use it commercially?**

A single `is_open_access` boolean cannot answer these questions.

## Access-state taxonomy

Initial conceptual access states:

- `OPEN_FULLTEXT`
- `OPEN_AUTHOR_MANUSCRIPT`
- `OPEN_PREPRINT`
- `PUBLICLY_VIEWABLE_RESTRICTED_REUSE`
- `USER_OWNED_COPY`
- `USER_INSTITUTIONAL_ACCESS`
- `USER_PUBLISHER_ACCESS`
- `METADATA_ONLY`
- `RESTRICTED`
- `UNAVAILABLE`
- `UNKNOWN`

These describe access context, not every permitted action.

## Rights actions

Policy should evaluate an explicit action enum such as:

- `DISCOVER_METADATA`
- `LINK_OUT`
- `RETRIEVE_TRANSIENT`
- `RETRIEVE_PERSIST`
- `DISPLAY_FULLTEXT`
- `CACHE_FULLTEXT`
- `TEXT_MINE`
- `CREATE_EMBEDDING`
- `GENERATE_SUMMARY`
- `GENERATE_EXPLANATION`
- `GENERATE_TTS`
- `STORE_DERIVATIVE`
- `REDISTRIBUTE_ORIGINAL`
- `REDISTRIBUTE_DERIVATIVE`
- `COMMERCIAL_USE`

## Decision model

Each decision should conceptually be:

```text
RightsDecision
├─ subject
├─ action
├─ decision: ALLOW | DENY | UNKNOWN | CONDITIONAL
├─ basis
├─ evidence
├─ entitlement_context
├─ jurisdiction_context (when applicable)
├─ policy_version
├─ evaluated_at
└─ expires_at / recheck_after
```

`UNKNOWN` must not silently become `ALLOW` for actions that require affirmative rights.

## Evidence precedence

Potential evidence sources include:

1. explicit license attached to the exact article/version;
2. publisher/repository rights metadata;
3. trusted access-resolution provider metadata;
4. source terms governing automated retrieval/reuse;
5. user entitlement or user-provided copy;
6. direct permission from rights holder;
7. internal policy/legal determination;
8. heuristic inference.

Heuristic inference should have lower confidence than direct license evidence.

## License normalization

Normalize known licenses (for example Creative Commons variants) while retaining the raw observed source value and evidence URL/record. Do not silently map ambiguous statements such as “free to read” to a permissive reuse license.

Important distinctions include:

- attribution requirements;
- commercial-use restrictions;
- derivative-work restrictions;
- share-alike requirements;
- text/data-mining provisions where separately defined;
- territory/jurisdiction if material.

## Open access is not synonymous with unrestricted reuse

A work may be readable without charge while the license still limits redistribution, derivatives, or commercial use. Conversely, some openly licensed works permit extensive reuse.

NOESYN must evaluate the actual action against the available rights evidence.

## Source terms matter independently

Even where underlying content may be reusable, an API or repository may impose technical or contractual constraints on automated access, rate, bulk retrieval, caching, attribution, or redistribution.

Source contracts should therefore record both:

- **content rights evidence**; and
- **provider/service terms**.

## User-authorized access

Future users may bring lawful access through:

- a document they upload;
- institutional/library authentication;
- publisher subscription;
- personal purchase;
- organizational license.

Rules:

1. A user entitlement is scoped to that user/session/organization as appropriate.
2. It does not convert the work into globally open content.
3. Derived artifacts must inherit restrictions where required.
4. Credentials must not be stored in source records or logs.
5. Browser-side or ephemeral processing should be preferred when it materially reduces redistribution/storage risk.

## Access resolver behavior

For a requested work, the resolver should collect candidate locations such as:

- publisher version of record;
- open publisher copy;
- PubMed Central / Europe PMC copy;
- institutional repository accepted manuscript;
- subject repository copy;
- arXiv/preprint;
- CORE-indexed repository copy;
- author-hosted lawful copy where policy accepts it;
- user-entitled publisher/institutional path.

Candidates are ranked only after rights evaluation for the requested operation.

## Example

A user asks to listen to a paper.

```text
1. Resolve work and preferred version.
2. Find candidate access locations.
3. Evaluate GENERATE_TTS for each candidate/artifact.
4. Prefer a lawful, high-quality structured version.
5. If no candidate permits TTS:
   - offer metadata/abstract-level features if permitted;
   - link to authorized external access;
   - allow user-authorized/uploaded processing if policy permits.
```

The system must not choose an unauthorized copy simply because it is technically downloadable.

## Artifact retention classes

Possible retention classes:

- `PUBLIC_REUSABLE` — safe to persist according to license/source terms;
- `PUBLIC_LIMITED` — persistence allowed but downstream actions restricted;
- `ENTITLEMENT_SCOPED` — available only inside a user/org entitlement context;
- `TRANSIENT_ONLY` — may be processed ephemerally but not retained;
- `METADATA_ONLY` — full text must not be acquired;
- `UNKNOWN_HOLD` — quarantine pending policy resolution.

## Derived-content inheritance

A generated summary, embedding, audio file, or extracted evidence set must keep a dependency link to the source artifact and a snapshot of the rights decision under which it was created.

If rights later change, NOESYN must be able to identify affected derived artifacts and determine whether they need to be hidden, regenerated, or deleted.

## Operational guardrails

- Never commit licensed full text to the public repository.
- Never log authentication tokens or institutional credentials.
- Do not scrape endpoints when a provider prohibits automated retrieval.
- Respect documented rate limits and bulk-access pathways.
- Cache only where permitted and with explicit retention policy.
- Record the policy version used for consequential rights decisions.
- Recheck stale access/rights assertions.

## Current source-specific facts to preserve in implementation planning

### Crossref

Crossref metadata is broadly available through its REST API, and Crossref recommends identified/polite access and caching. Abstracts can carry separate copyright considerations.

Official documentation:
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/
- https://www.crossref.org/documentation/retrieve-metadata/rest-api/access-and-authentication/

### Unpaywall

Unpaywall exposes DOI-level open-access resolution through API v2 and recommends its database snapshot for use beyond API-scale limits.

Official documentation:
- https://unpaywall.org/products/api

### PubMed Central

PMC explicitly warns that not all PMC content is available for text mining/reuse, licenses vary by article, and automated retrieval must use approved developer services such as PMC Cloud, OAI-PMH, E-Utilities, or BioC.

Official documentation:
- https://pmc.ncbi.nlm.nih.gov/tools/developers/
- https://pmc.ncbi.nlm.nih.gov/tools/oai/

### CORE

CORE provides metadata and full-text access across its open-access provider network, but product use must still comply with CORE terms and underlying rights metadata.

Official documentation:
- https://core.ac.uk/services/api

### Europe PMC

Europe PMC provides metadata and open-access content through developer APIs, OAI, and bulk services. Full-text availability differs by collection/right.

Official documentation:
- https://europepmc.org/developers

## Legal-review boundary

This document is an engineering policy model, not legal advice. Before public deployment, commercial-scale full-text processing, institutional authentication, or broad derivative-content distribution, NOESYN should receive qualified legal review of the intended workflows and applicable source/license terms.
