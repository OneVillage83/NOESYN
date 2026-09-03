# arXiv Source Contract

- **Status:** VERIFIED
- **Last verified:** 2026-09-03
- **Adapter owner:** NOESYN Source Integration / Document Acquisition
- **Adapter/schema version:** Phase 1A contract v1
- **Official documentation:**
  - https://info.arxiv.org/help/api/user-manual.html
  - https://info.arxiv.org/help/bulk_data.html
  - https://info.arxiv.org/help/license/index.html

## NOESYN role

arXiv is NOESYN's principal preprint/source-version provider for supported disciplines.

Use it for:

- arXiv identifier/version identity observations;
- preprint discovery;
- explicit version lineage (`v1`, `v2`, ...);
- metadata synchronization via API/OAI;
- PDF/source acquisition only under the exact applicable license/action policy;
- connecting preprints to later DOI/published WorkVersions.

arXiv is not a blanket open-license corpus. The default arXiv submission license permits arXiv to distribute the work but does not automatically grant third parties unrestricted redistribution rights.

## Identifiers/version semantics

- Canonical external form: arXiv identifier plus optional version suffix.
- Base arXiv ID identifies the submission lineage; `vN` identifies a specific version.
- NOESYN must model each materially distinct arXiv version as a WorkVersion/source version observation rather than overwriting prior text.
- `published`/submission date and latest `updated` date have different meanings.

## Operations

| Operation | Mechanism | Supported | Notes |
|---|---|---:|---|
| Search/list | arXiv API query | Yes | Atom feed. |
| Fetch by ID | API `id_list` / metadata | Yes | Preserve version semantics. |
| Incremental metadata | OAI-PMH | Yes | Preferred for bulk metadata. |
| Bulk metadata | OAI-PMH | Yes | Updated daily per current guidance. |
| PDF/source | arXiv export / official bulk mechanisms | Conditional | Rights/action specific. |
| Full corpus data | official AWS S3/Kaggle mechanisms | Conditional | Do not crawl corpus article-by-article. |

## API query semantics

The documented query API uses the arXiv export API with parameters such as:

- `search_query`;
- `id_list`;
- `start`;
- `max_results`;
- sort parameters.

Responses are Atom 1.0 feeds.

Current guidance encourages roughly a **3-second delay between repeated API calls**. The API documents result-volume limits; large metadata harvesting should use OAI-PMH instead of trying to page the query API through the entire corpus.

## Pagination/scale

Current API documentation describes an overall query result ceiling and bounded page sizes. Treat these limits as interactive/search constraints, not a bulk synchronization mechanism.

For bulk metadata, use OAI-PMH. For full corpus data, use the official bulk datasets/storage paths.

## Freshness/version semantics

arXiv is a strong test case for NOESYN's version-first architecture:

- `v1` must remain reproducible after `v2` arrives;
- derived summaries/audio/evidence must retain the exact arXiv version/artifact dependency;
- links to a published DOI create relationships between versions/works rather than silent replacement.

Each observation records arXiv ID/version, observed timestamp, provider timestamps, adapter version and payload/artifact hash.

## Bulk strategy

Official arXiv guidance directs bulk metadata users to OAI-PMH and bulk full-text/data users to official dataset mechanisms such as AWS S3/Kaggle rather than repeated API/PDF crawling.

NOESYN must never implement a crawler that attempts to download the complete arXiv corpus from article endpoints.

## Cache/storage policy and licenses

arXiv metadata is broadly reusable under the current metadata terms, but article content is separate.

Submissions can use different licenses, and even different versions of the same submission can carry different license choices. The default arXiv distribution license does **not** grant NOESYN a general right to redistribute the article.

Therefore content retention/action class is determined per version/license.

## Rights/license evidence

Preserve the exact license URI/selection associated with each version where exposed.

Possible states include the default arXiv license, Creative Commons variants, CC0 and other allowed options. The Rights Engine maps that exact license to requested actions.

For versions without sufficiently broad third-party rights, NOESYN can still index metadata and link users to arXiv while restricting local redistribution/derived full-text actions as required.

## Attribution

Retain arXiv identifier/version and author/source attribution. When displaying/linking an arXiv document, make the source/version visible and satisfy the exact content license attribution terms.

## Normalized mapping

- `source = arxiv`
- base arXiv ID
- explicit version suffix
- observation/provider timestamps
- raw metadata hash/reference
- adapter version
- title/authorship/category/comment/journal-reference candidates
- DOI/publication relationship candidates
- license observation by version
- artifact locator/hash when acquisition is authorized

## Data-quality caveats

- arXiv is preprint-centric and field coverage is selective.
- metadata can change between versions.
- a published DOI is not proof the arXiv and publisher versions are text-identical.
- licenses can vary by version.
- comments/journal references can be user-supplied and require normalization.

## Failure behavior

- provider throttle: honor delay/backoff; do not increase concurrency to compensate.
- query exceeds API limits: switch/refine query or use OAI/bulk mechanism.
- missing version: retain unresolved source observation; do not silently substitute latest version when an exact version was requested.
- artifact rights denied: return metadata/link with explicit content-action restriction.
- malformed Atom/PDF/source: quarantine and preserve source/hash where allowed.

## Test fixtures

1. arXiv submission with multiple versions;
2. default arXiv license;
3. Creative Commons licensed version;
4. version license change;
5. DOI/journal-reference relationship;
6. Atom search page;
7. OAI metadata record;
8. exact old-version request after newer version exists;
9. throttle/backoff behavior.

## Contract tests

- exact `vN` identity is preserved.
- requesting `v1` never silently returns `v2`.
- derived artifact dependency includes exact version/artifact hash.
- content license is version-scoped.
- default arXiv license does not automatically allow redistribution.
- API sync and OAI sync normalize into compatible source observations.

## Security notes

- Atom/XML is untrusted; safe parser settings required.
- source archives/PDFs are untrusted files and must be resource-limited.
- links do not bypass Acquisition SSRF policy.

## Change history

### 2026-09-03 — v1

Initial verified Phase 1A contract. Exact arXiv version identity and version-scoped licensing are frozen as critical requirements.