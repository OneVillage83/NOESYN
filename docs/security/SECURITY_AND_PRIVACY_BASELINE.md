# NOESYN Security & Privacy Baseline

## Purpose

Establish security and privacy boundaries before implementation begins, especially because NOESYN may eventually handle public scholarly metadata, copyrighted/licensed full text, institutional entitlements, user-uploaded documents, private annotations, and AI-provider integrations.

## Security domains

Treat the following as distinct domains:

1. public scholarly metadata;
2. openly reusable full text;
3. publicly viewable but reuse-restricted content;
4. entitlement-scoped content;
5. user-uploaded/private documents;
6. user personal research state;
7. credentials/secrets;
8. derived AI/audio artifacts;
9. operational logs/telemetry.

Authorization and retention rules differ across these domains.

## Public repository rule

`OneVillage83/NOESYN` is public.

Never commit:

- API keys;
- OAuth tokens;
- session cookies;
- publisher credentials;
- institutional/library credentials;
- `.env` secrets;
- production data exports;
- private user data;
- licensed full-text artifacts not permitted for redistribution;
- generated audio/derivatives whose source restrictions prohibit public redistribution.

## Secret handling

Implementation must use environment/runtime secret injection or a dedicated secret manager.

Required practices:

- `.env` ignored;
- `.env.example` contains names only, never real secrets;
- secret values never logged;
- credentials never stored inside source observations;
- rotate compromised secrets immediately;
- least-privilege scopes;
- separate development/staging/production credentials;
- automated secret scanning in CI when available.

## Authentication and authorization

Future architecture should separate:

- user authentication;
- resource authorization;
- external-source entitlements;
- administrative privileges.

Authorization decisions should be performed server-side for protected resources. UI hiding is not authorization.

## Entitlement boundary

Institutional or personal publisher access must remain scoped to the entitled user/organization/session.

A successful authenticated retrieval must not cause restricted source content to enter a globally readable cache.

Cache/storage keys for entitlement-scoped artifacts require access-control context.

## Object storage

Private/restricted object storage should be deny-public-by-default.

Use:

- random/non-enumerable object identifiers;
- short-lived signed access URLs where appropriate;
- encryption at rest;
- transport encryption;
- retention/lifecycle policy;
- provenance metadata stored separately from public URLs.

## Database isolation

Public corpus data and user-private state should have explicit logical separation. Sensitive user data should not be copied into analytics/search indexes unless required and governed.

## Search/vector indexes

Indexes can leak data just as databases can.

Before indexing private/restricted text:

- establish authorization-aware retrieval;
- record source/tenant/user scope;
- prevent cross-user retrieval;
- define deletion propagation;
- define embedding-provider privacy policy.

## AI-provider boundary

Documents are not automatically safe to send to an external model provider.

Before any provider integration, document:

- data sent;
- retention/training policy;
- regional processing if relevant;
- user consent expectations;
- source-rights implications;
- deletion/support procedures;
- fallback/local-processing options.

## Document prompt injection

All external documents are untrusted data.

A paper may contain text such as instructions, URLs, malicious markup, or adversarial prompts. The AI/tooling layer must never grant document text control over:

- system instructions;
- tool permissions;
- rights decisions;
- credential access;
- network targets;
- file writes;
- administrative operations.

Sanitize/render active content safely.

## SSRF and retrieval security

The acquisition layer will fetch external URLs and is therefore a potential SSRF boundary.

Requirements:

- validate schemes;
- block loopback/link-local/private network ranges unless explicitly needed;
- resolve redirect chains safely;
- cap redirects;
- constrain file size/time;
- validate content type;
- defend against DNS rebinding;
- use allowlists for provider adapters where possible;
- isolate retrieval workers from sensitive internal networks.

## File-upload security

User uploads may be malicious.

Requirements:

- size/type limits;
- malware scanning where appropriate;
- no direct execution;
- sandboxed parsers;
- decompression limits / zip-bomb protections;
- PDF/office active-content handling;
- content sniffing;
- per-user authorization;
- deletion support.

## Parser isolation

PDF/XML/image parsers process adversarial content. High-risk parsers should run with:

- no unnecessary network access;
- constrained CPU/memory/time;
- read-only base filesystem where practical;
- ephemeral working directories;
- maintained dependency versions.

## XML security

Disable unsafe external entity resolution and related XML expansion attacks. JATS/XML support must not introduce XXE or entity-expansion vulnerabilities.

## Web application baseline

Before public deployment:

- CSRF protection where applicable;
- XSS-safe rendering;
- CSP;
- secure cookies;
- rate limiting;
- dependency vulnerability scanning;
- strict input validation;
- authorization tests;
- secure redirect handling;
- audit logging for sensitive actions.

## Privacy principles

Collect the least user data necessary for product functionality.

Potential sensitive research behavior includes:

- papers read;
- search history;
- private annotations;
- uploaded documents;
- institutional affiliations/entitlements;
- generated questions;
- listening history.

Treat these as private user data rather than default public analytics.

## Data lifecycle

Each data class should eventually define:

- purpose;
- legal/policy basis;
- retention period;
- deletion behavior;
- backup behavior;
- derived/index deletion propagation;
- user export behavior;
- audit requirements.

## Logging

Never log raw secrets. Avoid raw full-text/user-document logging unless explicitly required for a controlled debugging workflow.

Prefer structured identifiers and redacted error context.

## Audit/provenance versus privacy

Provenance requirements do not justify retaining unnecessary private content forever. Design provenance records so that restricted artifacts can be deleted while preserving non-content audit facts where policy permits (hashes, timestamps, source ID, policy decision, etc.).

## Dependency/supply-chain policy

Before production implementation:

- pin/lock dependencies;
- automated dependency scanning;
- review install scripts/native binaries;
- protect CI credentials;
- restrict third-party GitHub Actions to reviewed/pinned versions;
- generate SBOM where useful;
- establish patch cadence.

## Backups

Backups containing restricted/private data inherit the same sensitivity. Deletion/retention policy must account for backups.

## Incident readiness

Before public beta, define:

- security contact;
- credential compromise response;
- user-data incident response;
- provider-key rotation;
- artifact takedown process;
- rights complaint workflow;
- audit-log preservation;
- post-incident documentation.

## Phase 0 invariant

NOESYN must never treat “research paper” as synonymous with “public, harmless data.” Content may be copyrighted, confidential to a user, adversarial to a parser/model, or accessible only through an entitlement. Security and rights are enforced through the complete artifact lifecycle.
