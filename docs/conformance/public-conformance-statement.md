# Public Conformance Statement

> **Document status:** Current public claim boundary. Last packaged September 2026.

ForgeDITA is a standards-first DITA CCMS. The product rule is:

ForgeDITA conforms to the DITA features listed in this public conformance statement. Features not listed here are not claimed as complete, even if partial implementation exists.

This statement is intentionally conservative. It is a product contract, not marketing copy.

## Current Conformance Track

- Primary track: DITA 1.3
- DITA 2.0: not claimed
- Storage model: native XML and binary assets remain unchanged; ForgeDITA does not convert DITA into a proprietary canonical format.
- Toolchain model: tenant-owned toolchain bundles provide catalogs, validation assets, semantic-registry artifacts, publishing profiles, and DITA-OT execution context.

## Currently Claimed

### Native Content Storage

ForgeDITA currently claims support for storing and retrieving the following as native repository objects:

- DITA topics as XML
- DITA maps as XML
- Subject scheme maps as XML
- DITAVAL files as XML
- XML catalogs and grammar-related XML assets
- Binary assets as object-store-backed repository nodes

Evidence:

- Repository nodes and revisions preserve source content.
- Postgres revision bodies, working-copy bodies, binary assets, and generated artifacts use object-store-backed payloads.
- Repository export emits native files.
- ForgeDITA package import validates checksums and byte lengths before writes.
- Loose DITA ZIP import accepts normal DITA archives without requiring ForgeDITA metadata.

### Authoring API

ForgeDITA currently claims API support for:

- Repository tree browsing
- Node creation
- Raw content retrieval
- Check-out
- Working-copy save
- Check-in
- Cancel check-out
- Revision listing
- Binary asset upload and retrieval

### Validation

ForgeDITA currently claims beta-level validation coordination across:

- DITA-OT-backed validation
- Tenant toolchain validation assets
- Catalog artifacts
- Grammar artifacts
- Schematron artifacts
- Policy-rule diagnostics

This is not yet a full DITA 1.3 conformance claim for every grammar, specialization, profile, and subject-scheme interaction.

### Graph And Resolution

ForgeDITA currently claims executable support for:

- Semantic-registry-backed graph extraction
- Raw outbound reference inspection
- Raw inbound reference inspection
- Map-context snapshots
- Key definition derivation
- Basic key lookup from a map context
- Basic impact analysis

This is not yet a complete DITA 1.3 resolution claim for every conref, conkeyref, key scope, filtering, subject scheme, and specialization interaction.

### Publishing

ForgeDITA currently claims beta-level support for:

- Preview jobs
- Publish jobs
- queued DITA-OT-backed execution with runtime confinement
- Runtime manifests
- Logs, diagnostics, output artifacts, and object-store-backed job artifacts
- Tenant-owned toolchain bundle selection and promotion controls
- Signed artifact retrieval, cancellation, and retry paths
- Release/publish gating through the `release.requireReleaseEligible` tenant policy

This is not an independent production-isolation certification. Worker, queue, runtime-broker, cancellation, retry, manifest, and confinement paths exist at the current beta gate, while deployment-specific sandbox and scale acceptance remain active work.

### Workflow And Release Evidence

ForgeDITA currently claims beta workflow support for:

- Node workflow state
- Assignments and assignment history
- Submit, approve, reject, comment, and governed transition operations
- Separation-of-duties enforcement for approval
- Release eligibility
- External evidence references
- Workflow comments
- Audit events for workflow transitions and comments
- Optional tenant release gate policy

ForgeDITA does not currently claim to replace dedicated regulated approval systems such as DOORS NG, Polarion, Jira, or ServiceNow.

### Search

ForgeDITA currently claims:

- Workspace search
- Postgres search-document indexing
- Rebuild support
- Paging metadata
- Match/snippet provenance
- DITA, profile, taxonomy, and semantic facets
- Graph-derived inbound and outbound facets
- Baseline- and release-constrained views

Representative-corpus scale, relevance tuning, and complete subject-scheme behavior remain active beta work.

### Capability Discovery

ForgeDITA currently exposes:

- `GET /api/v1/capabilities`

This endpoint reports API version, DITA track, storage provider, feature groups, supported import types, and tenant policy keys.

## Explicitly Not Yet Claimed

ForgeDITA does not yet claim complete support for:

- All DITA 1.3 specialization interactions
- Complete conref/conkeyref/keyref/keyscope resolution behavior
- Complete DITAVAL and subject-scheme-aware validation
- Branch-aware repository semantics
- DITA 2.0
- independently certified production runtime isolation
- broad cloud-provider repeatability beyond the current S3-compatible MinIO evidence
- final production Oxygen add-on acceptance
- semantic XML merge behavior beyond explicit path-level import modes
- universal DITA 1.3 conformance

## Conformance Test Strategy

Each claimed feature should have executable evidence before it is promoted from partial to claimed-complete:

- API smoke coverage for the endpoint or behavior
- Repository fixture coverage for the content shape
- Tenant toolchain fixture coverage where catalogs, grammars, Schematron, or semantic registry rules affect behavior
- Negative tests for malformed input, checksum mismatches, missing scope, and authorization failures
- Regression fixtures for common interoperability sources such as loose DITA ZIP exports

The current working matrix is maintained in the [Conformance Matrix](conformance-matrix.md).

## Product Rule

If ForgeDITA cannot preserve standards-compliant DITA without proprietary transformation or hidden professional-services dependency, the feature is not complete.
