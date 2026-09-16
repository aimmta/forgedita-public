# Conformance Matrix

> **Document status:** Working matrix. A checked or described implementation path is not a public completeness claim unless it is also included in the [Public Conformance Statement](public-conformance-statement.md).

This matrix tracks the current ForgeDITA conformance claim against executable or documented evidence.

Statuses:

- `Claimed`: covered by current implementation and smoke evidence at prototype level
- `Partial`: implemented in part, but not complete enough for a broad conformance claim
- `Not Claimed`: explicitly outside the current public claim

## DITA 1.3 Track

| Area | Status | Current Evidence | Remaining Work |
|---|---|---|---|
| Native XML storage | Claimed | Repository nodes/revisions preserve XML; object-store-backed revision bodies; raw content retrieval | Remove legacy inline fallback after migration confidence |
| Binary asset storage | Partial | Asset nodes with object-store-backed payloads; binary upload/retrieval smoke coverage | Streaming upload, range reads, richer media metadata |
| DITA topic storage | Claimed | `.dita` and XML topic nodes preserve source content | Broader fixture corpus |
| DITA map storage | Claimed | Map nodes preserve source content; map context builds exist | Broader map fixture corpus |
| Subject scheme storage | Claimed | Subject-scheme node type exists; semantic architecture accounts for subject schemes | Context-aware subject-scheme validation and graph use |
| DITAVAL storage | Claimed | DITAVAL node type exists and is accepted as XML | Profile-aware validation and publish selection |
| XML catalog assets | Partial | Catalog/toolchain artifacts exist | Full catalog resolution fixture coverage |
| Grammar assets | Partial | Grammar/toolchain artifacts exist | Full DTD/RNG/XSD fixture coverage |
| Schematron assets | Partial | Schematron/toolchain artifacts exist and participate in validation coordination | Richer Schematron diagnostics and fixture coverage |
| Specialization-aware semantics | Partial | Semantic registry exists and is shared by graph/validation paths | Larger specialization corpus and conformance fixtures |
| Key reference extraction | Partial | Graph extraction captures key-related facts; map context derives key definitions | Complete keyscope and ambiguity behavior |
| Conref/conkeyref extraction | Partial | Graph extraction captures raw reference facts | Complete resolution and validation behavior |
| Xref/topicref extraction | Partial | Raw inbound/outbound reference APIs exist | Complete branch/profile/baseline-aware views |
| Map context | Partial | Map context snapshots and entries are persisted | Baseline-aware and profile-aware context builds |
| Impact analysis | Partial | Node/revision impact endpoints exist | Release-aware and dependency-classified impact |
| DITA-OT validation | Partial | DITA-OT-backed validation adapter exists | Full DITA 1.3 validation matrix |
| Tenant validation assets | Partial | Bundle catalogs, grammars, Schematron, and policy artifacts participate in validation | Subject-scheme/profile-aware validation |
| Preview | Partial | DITA-OT-backed preview jobs run in local temp workspaces | Hardened async worker and stronger sandbox |
| Publish | Partial | DITA-OT-backed publish jobs produce output, logs, diagnostics, manifests | Hardened async worker, cancellation, retries |
| Release reproducibility | Partial | Runtime manifests pin baseline revisions, bundle/profile, hashes, semantic registry | Rebuild verification tests |
| Release eligibility gate | Claimed | Tenant policy `release.requireReleaseEligible`; smoke covers default-off and enabled rejection | UI/admin polish and external sync hooks |
| Workflow evidence | Claimed | Node state, release eligibility, comments, external evidence, audit events | Optional integrations with external approval systems |
| Repository export | Claimed | ZIP export with native files and manifest metadata | Broader interoperability fixtures |
| ForgeDITA package import | Claimed | Manifest checksum/byte-length preflight, dry-run, rollback | Merge/update modes |
| Loose DITA ZIP import | Partial | No ForgeDITA manifest required; root-map detection; dry-run; multi-root guard | AEM Guides/Oxygen/Git fixtures and merge/update modes |
| Search | Partial | Postgres search documents, paging, match metadata, first DITA facets | Production ranking, highlighting, taxonomy and graph facets |
| Capability discovery | Claimed | `GET /api/v1/capabilities` | Versioned compatibility contracts |

## Not Claimed Yet

| Area | Status | Reason |
|---|---|---|
| DITA 2.0 | Not Claimed | Future preview lane only |
| Complete DITA 1.3 specialization conformance | Not Claimed | Requires full fixture corpus and generated matrix |
| Complete keyscope/conkeyref/conref behavior | Not Claimed | Resolution layer is still partial |
| Branch-aware repositories | Not Claimed | Deferred from MVP |
| Production-grade object storage | Not Claimed | Filesystem object store only |
| Production-grade isolated workers | Not Claimed | Local temp runtime only |
| Oxygen add-on package | Not Claimed | API foundation exists; add-on not packaged |

## Matrix Maintenance Rule

A row can move from `Partial` to `Claimed` only when the feature has fixture coverage, smoke or conformance test evidence, and no known architecture contradiction in the backlog.
