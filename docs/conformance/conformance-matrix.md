# Conformance Matrix

> **Document status:** Working matrix. A checked or described implementation path is not a public completeness claim unless it is also included in the [Public Conformance Statement](public-conformance-statement.md).

This matrix tracks the current ForgeDITA conformance claim against executable or documented evidence.

Statuses:

- `Claimed`: covered by current beta implementation and executable evidence
- `Partial`: implemented in part, but not complete enough for a broad conformance claim
- `Not Claimed`: explicitly outside the current public claim

## DITA 1.3 Track

| Area | Status | Current Evidence | Remaining Work |
|---|---|---|---|
| Native XML storage | Claimed | Repository nodes/revisions preserve XML; object-store-backed revision bodies; raw content retrieval | Remove legacy inline fallback after migration confidence |
| Binary asset storage | Claimed | Object-store-backed asset nodes, upload/retrieval, byte-range reads, and integrity checks | Broader cloud-provider repeatability and richer media metadata |
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
| Map context | Partial | Persisted, baseline-aware map contexts and context-sensitive resolution results | Complete profile, scope, and filtering behavior |
| Impact analysis | Partial | Context-aware inbound/outbound and impact views exist | Complete dependency classification and filtered-release semantics |
| DITA-OT validation | Partial | DITA-OT-backed validation adapter exists | Full DITA 1.3 validation matrix |
| Tenant validation assets | Partial | Bundle catalogs, grammars, Schematron, and policy artifacts participate in validation | Subject-scheme/profile-aware validation |
| Preview | Partial | Queued DITA-OT preview jobs, signed artifacts, cancellation, retry, and runtime confinement | Production-environment sandbox acceptance |
| Publish | Partial | Queued DITA-OT publish jobs produce artifacts, logs, diagnostics, manifests, and governed retrieval | Production-environment sandbox and scale acceptance |
| Release reproducibility | Partial | Runtime manifests pin baseline, bundle, profile, semantic registry, artifact inventory, and rebuild fingerprint; rebuild evidence exists | Broader corpus and environment repeatability |
| Release eligibility gate | Claimed | Tenant policy `release.requireReleaseEligible`; smoke covers default-off and enabled rejection | UI/admin polish and external sync hooks |
| Workflow evidence | Claimed | First-class workflow actions, assignments, comments, separation-of-duties enforcement, release eligibility, external evidence, and audit events | Optional integrations with external approval systems |
| Repository export | Claimed | ZIP export with native files and manifest metadata | Broader interoperability fixtures |
| ForgeDITA package import | Claimed | Manifest checksum/byte-length preflight, dry-run, rollback | Merge/update modes |
| Loose DITA ZIP import | Partial | Direct and staged size-aware import, root-map detection, dry-run analysis, explicit path modes, and graph-extraction prompt | Broader sanitized vendor packages and semantic merge policy |
| Search | Partial | Postgres ranking, highlights, paging, DITA/profile/taxonomy facets, graph facets, and baseline/release views | Representative-corpus scale and relevance tuning |
| Capability discovery | Claimed | `GET /api/v1/capabilities` | Versioned compatibility contracts |

## Not Claimed Yet

| Area | Status | Reason |
|---|---|---|
| DITA 2.0 | Not Claimed | Future preview lane only |
| Complete DITA 1.3 specialization conformance | Not Claimed | Requires full fixture corpus and generated matrix |
| Complete keyscope/conkeyref/conref behavior | Not Claimed | Resolution layer is still partial |
| Branch-aware repositories | Not Claimed | Deferred beyond the current beta scope |
| Broad cloud-provider object-storage readiness | Not Claimed | S3-compatible paths are validated with MinIO; broader provider repeatability remains open |
| Independently certified production runtime isolation | Not Claimed | Worker, broker, and container-oriented confinement exist at beta; certification is not claimed |
| Production-accepted Oxygen add-on | Not Claimed | Packaged beta add-on and update-site artifacts exist; final task-by-task acceptance remains open |

## Matrix Maintenance Rule

A row can move from `Partial` to `Claimed` only when the feature has fixture coverage, smoke or conformance test evidence, and no known architecture contradiction in the backlog.
