# Product Status

Last updated: September 2026

ForgeDITA is in active beta. The MVP and release-candidate milestones are complete, and the product has progressed through core beta API, web UI, deployment, import, conformance, and editor-integration work. This page separates beta capability from completed production acceptance.

## Current Claims

The current public claim boundary is maintained in the [Public Conformance Statement](docs/conformance/public-conformance-statement.md). Beta implementation includes native repository objects, repository and content APIs, validation coordination, graph extraction and contextual resolution, baseline and release operations, queued preview and publish jobs, package-registry operations, workflow APIs, webhooks, search, bulk import, capability discovery, a client-facing web UI, and an Oxygen add-on package under final acceptance.

## Completed Beta Foundations

The following foundations have passed their current beta gates:

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

## Active Beta Work

- complete production Oxygen add-on acceptance and authoring UX evidence
- close active editor state, validation, and path-identity edge cases
- expand contextual resolution for scope inheritance, key scopes, conrefs, conkeyrefs, filtering, and subject schemes
- add broader sanitized migration packages and interoperability fixtures
- complete hosted identity-provider and deployment-environment evidence
- deepen cloud-provider repeatability and production sandbox evidence
- continue conformance, accessibility, performance, and adversarial QA

## Explicitly Not Claimed

- universal DITA 1.3 conformance
- DITA 2.0 conformance
- replacement of dedicated regulated approval systems
- complete branch-aware repository semantics
- complete conref, conkeyref, keyref, and key-scope behavior
- production certification or independent security attestation

## Reading Architecture Documents

Architecture documents describe the intended operating model. They are design contracts and review material, not proof that every described behavior is currently implemented.

Beta status means the product is available for controlled evaluation. It does not mean general availability, independent security certification, or complete DITA 1.3 conformance.
