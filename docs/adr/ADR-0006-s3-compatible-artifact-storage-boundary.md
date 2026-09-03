# ADR-0006: S3-Compatible Artifact Storage Boundary

- **Status:** Accepted
- **Date:** 2026-09-03
- **Decision owners:** NOESYN project
- **Scope:** Binary/raw artifact storage interface

## Context

NOESYN will eventually store or transiently process multiple artifact classes:

- reusable scholarly PDFs/XML/source archives;
- restricted or entitlement-scoped documents;
- user-private uploads;
- extracted figures/supplementary files;
- generated audio;
- parser/evaluation fixtures;
- raw source payloads where permitted.

These objects require private-by-default storage, signed access, lifecycle policy, content hashes and rights-specific retention. They should not be stored as large canonical database blobs or tied to one cloud vendor's identity model.

## Decision

Standardize NOESYN's object-storage boundary on an **S3-compatible object API/semantics**, while deferring the production storage vendor.

Required application-level capabilities:

- private by default;
- encryption at rest;
- object checksums/content hashes and immutable artifact identity;
- time-limited signed access;
- lifecycle/retention controls;
- metadata/tags sufficient to associate storage class and rights scope;
- clear separation of reusable, restricted, user-private and generated artifact policy domains;
- local-development compatible implementation.

Canonical database rows store artifact identity, hash, provenance, rights class and opaque storage locator/key—not a public cloud URL as identity.

## Production/local implementations

AWS S3 is an acceptable production implementation and currently provides encryption-at-rest defaults and presigned URL mechanisms, but vendor selection remains operationally deferred.

Local development may use an S3-compatible service so application behavior remains close to production.

## Rights rule

Object storage capability never decides whether an object may be retained or served. The Rights Engine provides the applicable action/retention decision; storage enforces that policy.

A reusable OA artifact and a user-entitled restricted artifact may use the same technical storage protocol while living under different ACL/lifecycle/policy boundaries.

## Consequences

### Positive

- application code is not coupled to local disk or one cloud URL format;
- artifacts can be content-addressed/hashed independently from provider URLs;
- signed access supports secure reader/audio delivery;
- lifecycle policies support rights-specific retention;
- easy future migration between compatible providers.

### Costs

- requires object-storage service even in some integration environments;
- S3-compatible implementations can differ in edge behavior, so contract tests are required;
- database/object transaction boundaries require explicit orphan/reconciliation handling.

## Rejected alternative: local filesystem as architecture

Useful for tiny tests but not acceptable as the production abstraction because it lacks portable ACL/lifecycle/signed-access semantics and encourages artifact paths to leak into domain identity.

## Rejected alternative: store all binary content in PostgreSQL

Rejected because large scientific documents/audio and lifecycle/streaming patterns are better suited to object storage; PostgreSQL remains the canonical metadata/provenance store.

## Review triggers

Review if a required artifact workload cannot be represented by the S3-compatible boundary or if a future storage technology offers materially better guarantees without leaking vendor identity into the domain model.