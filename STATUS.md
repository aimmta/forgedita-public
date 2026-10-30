# Product Status

Last reviewed: October 29, 2026

ForgeDITA is in active beta for controlled evaluation. Completed MVP foundations and core beta API, web UI, deployment packaging, import, conformance-publication, and editor-integration work retain their accepted scope. Release-candidate acceptance remains in progress. Completed technical gates do not establish production readiness or authorize customer release.

## Current Claims

The current public claim boundary is maintained in the [Public Conformance Statement](docs/conformance/public-conformance-statement.md). Beta implementation includes native repository objects, repository and content APIs, validation coordination, graph extraction and contextual resolution, baseline and release operations, queued preview and publish jobs, package-registry operations, workflow APIs, webhooks, search, bulk import, capability discovery, a client-facing web UI, and an Oxygen add-on package. Manual Oxygen UX acceptance for the frozen 0.3.98 package and browser application acceptance at the agreed scope are complete; package promotion and overall release-candidate acceptance remain separate decisions.

## Completed Beta Foundations

The following implemented foundations retain their existing beta evidence and accepted scope; this list is not an overall release-candidate or production acceptance decision:

- production-oriented authentication and tenant-management contracts
- repository, content, baseline, release, package-registry, workflow, and webhook APIs
- OIDC-capable web UI flows and administrative surfaces
- Docker Compose deployment packaging with Postgres, MinIO/S3, workers, webhook dispatch, metrics, and optional NATS JetStream
- layered validation using tenant toolchain assets
- semantic-registry-backed graph extraction, map context, resolution, and impact analysis
- queued DITA-OT preview and publish with manifests, signed artifacts, retry, cancellation, and runtime confinement
- Postgres search with ranking, highlights, DITA metadata, graph facets, and baseline/release views
- size-aware bulk import with dry-run analysis and post-import graph prompts
- generated conformance statement and evidence publication

## Latest Technical Evidence And Acceptance Boundary

Implementation CI run `40809721638`, candidate `4426d2cfb810361c6b96f4a2bced7eb050a3fc18`, completed successfully on October 29 UTC (October 28 EDT). Its latest attempt reports:

| Executed job | Result |
|---|---|
| Core regression and frontend build | Success |
| Admin Portal production-shaped browser acceptance | Success |
| Postgres P0 smoke, including lease regression | Success |
| Postgres provider documentation acceptance | Success on attempt 2; attempt 1 failed |
| Deployment contract | Success |
| Conformance publication | Success |
| Hosted IdP smoke | Skipped; no hosted IdP pass claimed |

The run and artifacts are in the private implementation repository. These are CI job outcomes, not an independent review of every artifact or a formal QA closeout. Earlier failed attempts remain historical evidence. This update does not promote or replace the public conformance matrix with new feature claims.

The latest committed QA handoff still records security/conformance acceptance as **NO-GO**, with integrated-evidence acceptance (`R-264`) and the overall beta release-candidate gate (`R-053`) open. The later successful CI run supplies new evidence for QA to assess; no subsequent documented QA acceptance closing those gates was found as of this review. Completed browser and Oxygen acceptance retains its actual scope.

## Remaining Release-Candidate Acceptance

- QA review and acceptance of the integrated CI artifacts and release-profile security/conformance conclusions
- deployment/runbook and representative-workload performance acceptance
- independent customer onboarding, usable evaluation deployment, and candidate-specific release notes and limitations
- consolidated QA recommendation and the product owner's beta-candidate/release decision

These are the existing release requirements, not new gates. CI success alone does not establish their completion.

## Continuing Beta Limits And Evaluation Work

- deeper contextual DITA scope, filtering, subject-scheme, and specialization coverage
- representative-corpus scale and search relevance evidence
- broader sanitized migration packages and interoperability fixtures
- hosted identity-provider evidence; the current CI smoke was skipped
- broader cloud-provider repeatability and deployment-specific sandbox evidence
- conformance, accessibility, performance, and adversarial QA within the agreed beta scope

## Explicitly Not Claimed

- universal DITA 1.3 conformance
- DITA 2.0 conformance
- replacement of dedicated regulated approval systems
- complete branch-aware repository semantics
- complete conref, conkeyref, keyref, and key-scope behavior
- production certification or independent security attestation

## Reading Architecture Documents

Architecture documents describe the intended operating model. They are design contracts and review material, not proof that every described behavior is currently implemented.

Beta status means the product is available for controlled evaluation. It does not mean completed release-candidate acceptance, general availability, production release approval, independent security certification, or complete DITA 1.3 conformance.

