# Extensibility Architecture

> **Document status:** Target architecture and governance model. Current product claims are limited to the [Public Conformance Statement](../conformance/public-conformance-statement.md).

This document defines how ForgeDITA supports extension as a platform, with special attention to **semantic extensibility**.

It cross-references:

- [Reference Architecture](reference-architecture.md)
- [Graph Resolution Architecture](../content-model/graph-and-resolution.md)

The purpose of this artifact is to answer one architectural question clearly:

How does ForgeDITA remain DITA-native, tenant-safe, and reproducible while still allowing tenants to extend semantics, validation, publishing, and editor behavior?

## Why This Document Exists

Many systems claim extensibility when they really mean:

- users can upload files
- admins can tweak transforms
- vendors can write custom code for special cases

That is not enough for a multitenant DITA CCMS.

ForgeDITA needs a stronger model:

- extensibility must be explicit
- extension scope must be controlled
- extension semantics must be versioned
- extension behavior must be reproducible
- extension boundaries must not undermine DITA conformance claims

The hardest part is **semantic extensibility**:

- specialized elements
- specialized attributes
- new reference-bearing attributes
- key-like and conref-like semantics
- custom map semantics
- subject-scheme-driven behavior

This is the place where CCMS products often fail. They support DITA until a tenant actually extends DITA in a meaningful way, and then the system falls back to hard-coded base assumptions.

ForgeDITA must not do that.

## Relationship To The Overall Architecture

This document fits directly into the services and models already defined elsewhere.

### Reference Architecture alignment

From the [Reference Architecture](reference-architecture.md):

- `Toolchain registry` is where extension packages live
- `Validation service` consumes extension-aware validation assets
- `Graph and resolution service` consumes extension-aware semantic rules
- `Publish orchestrator` runs extension-aware publishing stacks in isolation
- `Oxygen integration` consumes extension-aware frameworks and diagnostics

This document does not replace those services. It defines the extension contract they share.

### Graph Resolution alignment

From the [Graph Resolution Architecture](../content-model/graph-and-resolution.md):

- graph extraction must be specialization-aware
- raw graph facts must be separate from context facts and resolved outcomes
- reference semantics cannot be derived from hard-coded base names alone

This document adds the missing piece:

- how those semantics are declared, versioned, and loaded into the system

## Extensibility Goals

ForgeDITA should support:

- tenant-specific DITA specializations
- tenant-specific grammars, catalogs, Schematron, and subject schemes
- tenant-specific publishing plug-ins and profiles
- tenant-specific graph extraction semantics
- tenant-specific editor frameworks and templates
- tenant-specific workflow and policy validations

ForgeDITA should avoid:

- global mutable customization
- runtime behaviors that depend on undocumented conventions
- vendor-private semantic rules not expressible in exported bundle artifacts
- extension models that only work in one editor

## Categories Of Extension

Not all extension is equally dangerous. We need to treat them differently.

### 1. Presentation extensions

Examples:

- CSS
- PDF themes
- HTML templates
- Oxygen Author CSS
- editor templates

Risk:

- low to medium

Handling:

- package as toolchain artifacts
- isolate by tenant bundle version
- safe to defer from core semantic processing

### 2. Validation extensions

Examples:

- RNG/XSD/DTD
- catalogs
- Schematron
- DITAVAL presets
- subject schemes
- tenant policy rule sets

Risk:

- medium

Handling:

- package as tenant-scoped validation assets
- version with toolchain bundle
- expose diagnostics with stable source attribution

### 3. Semantic extensions

Examples:

- specialized elements
- specialized attributes
- new reference-bearing attributes
- new map-scoped semantic constructs
- key-like or conref-like semantics

Risk:

- very high

Handling:

- must be explicitly declared through a semantic registry model
- must be consumable by graph extraction, resolution, validation, preview, and publishing
- must never rely only on UI or plugin assumptions

### 4. Process extensions

Examples:

- publish profiles
- preview pipelines
- custom build steps
- post-processing steps

Risk:

- high

Handling:

- run only through isolated build runtimes
- version and pin in toolchain bundles
- expose execution metadata in jobs/releases

### 5. Integration extensions

Examples:

- Oxygen framework packs
- webhooks
- editor actions
- import/export adapters

Risk:

- medium

Handling:

- API-first
- capability/version negotiation
- never make integration define core semantics on its own

## Architectural Principle: Semantics Must Be Declared

This is the core rule.

ForgeDITA must distinguish between:

- content grammar
- validation assets
- semantic interpretation rules
- publishing behavior

In many systems, those get blurred together.

For example:

- a grammar may permit an attribute
- a Schematron rule may validate it
- a transform may use it
- but the graph layer may still not know it is a reference

That is the failure mode we must prevent.

So the architecture should require that any tenant extension with platform-visible semantics be declared in a **semantic registry** that is packaged inside the tenant toolchain bundle.

## Extension Scopes

Every extension must declare its scope.

### Platform scope

Owned by ForgeDITA itself.

Examples:

- built-in base DITA semantics
- core API contracts
- built-in extractor/resolver behavior

Rules:

- platform scope changes only by product release
- platform scope must remain exportable and documented

### Tenant scope

Owned by one tenant bundle.

Examples:

- custom grammars
- custom catalogs
- custom semantic registry
- custom validation rules
- custom publish plugins

Rules:

- tenant scope cannot leak into another tenant
- tenant scope is immutable once bundle is promoted

### Workspace scope

Allowed only for configuration selection, not semantic invention.

Examples:

- choosing one approved toolchain bundle over another
- selecting a publish profile
- selecting a subject scheme or ditaval preset from approved assets

Rules:

- workspace scope may choose among tenant-approved assets
- workspace scope should not define new core semantic interpretation rules

This separation matters because otherwise semantics become operationally ungovernable.

## Toolchain Bundle Model For Extension

The bundle model needs to make artifacts declare meaning, not merely exist.

Each tenant bundle should contain:

- grammars
- catalogs
- Schematron
- DITAVAL presets
- subject schemes
- publishing plugins
- Oxygen framework assets
- semantic registry

### Proposed artifact types

In addition to the base artifact inventory, the bundle model should support:

- `semantic-registry`
- `subject-scheme`
- `grammar`
- `catalog`
- `schematron`
- `editor-framework`
- `publish-plugin`
- `validation-config`
- `resolution-policy`

### Required manifest linkages

The bundle manifest should declare:

- which grammar modules are authoritative
- which catalogs resolve them
- which semantic registry version applies
- which validation assets are active
- which editor framework pack matches the semantics

This makes extension discoverable rather than implicit.

## Semantic Registry

This is the most important new architectural component.

The semantic registry is a tenant-scoped, versioned declaration of how ForgeDITA should interpret extension semantics.

It should not replace DITA grammars. It complements them.

The governing implementation model is the [Semantic Registry Model](../content-model/semantic-registry.md). This section preserves the architectural rationale; the dedicated model defines the concrete registry shape, matching rules, compatibility rules, service consumers, provenance requirements, and implementation gaps.

### Purpose

The semantic registry tells the platform:

- which elements or attributes are reference-bearing
- what kind of reference they represent
- whether they participate in graph extraction
- whether they require map context for resolution
- whether they define keys
- whether they bind subject schemes
- whether they introduce workflow/policy semantics

### Why grammars are not enough

Grammar tells us:

- what is allowed syntactically

Grammar does not fully tell us:

- how the graph should classify a reference
- whether resolution is direct, key-based, or conref-based
- whether a semantic rule is local, contextual, or publish-time

That extra meaning must be declared.

### Proposed registry structure

Minimal conceptual shape:

```yaml
apiVersion: forgedita/v1
kind: SemanticRegistry
metadata:
  tenant: tenant-acme
  bundle: toolchain-acme-core
  version: 2026.04.1
spec:
  references:
    - match:
        element: topicref
        attribute: href
      semantics:
        kind: direct-uri
        participatesInGraph: true
        requiresContext: false
    - match:
        attribute: keyref
      semantics:
        kind: key-reference
        participatesInGraph: true
        requiresContext: true
  keyDefinitions:
    - match:
        attribute: keys
      semantics:
        definesKeys: true
  subjectSchemeBindings:
    - match:
        element: subjectScheme
      semantics:
        bindsSchemes: true
```

This is only a starting point. The point is not the exact YAML; the point is that semantics become declarative and versioned.

### Matching model

The registry should support:

- element name
- attribute name
- element plus attribute pair
- later, specialization-aware matching via class ancestry

Long-term, matching should be driven by specialization metadata rather than only literal names.

## Specialization-Aware Processing

ForgeDITA cannot be truly extensible if it only understands base DITA names.

The system needs a specialization-aware processing model in three layers.

### Layer 1: grammar and catalog layer

Purpose:

- accept valid specialized documents

Handled by:

- tenant bundle grammars and catalogs

### Layer 2: semantic registry layer

Purpose:

- tell the platform what specialized constructs mean

Handled by:

- tenant semantic registry artifact

### Layer 3: processor behavior layer

Purpose:

- make graph extraction, validation orchestration, resolution, preview, and publish honor those semantics

Handled by:

- graph extractor
- resolution engine
- validation coordinator
- isolated build runtime

If any of those three layers is missing, extensibility is only partial.

## Subject Schemes And Semantic Extensibility

This is an area that causes confusion and must be kept explicit.

### What subject schemes can do

- constrain values
- classify values
- define controlled vocabularies
- influence validation and contextual semantics

### What subject schemes should not do alone

- invent new reference semantics by themselves
- replace specialization declarations
- silently teach the graph layer that an arbitrary new attribute is key-like or conref-like

So if a tenant wants a new attribute to behave like `keyref`, that should come from:

- specialization or grammar support
- semantic registry declaration
- validation and editor support

Subject schemes may then constrain the values used by that attribute, but they should not be the sole source of its reference semantics.

## Validation Extensibility

Validation should be orchestrated in layers.

### Proposed layered flow

1. XML well-formedness
2. grammar validation through tenant catalogs/grammars
3. semantic registry sanity checks
4. Schematron and tenant policy rules
5. subject-scheme-aware checks
6. optional context-aware validation using map context

### Why this matters

If validation knows about an extension but graph extraction does not, users get contradictory system behavior.

So validation and graph extraction must both consume the same semantic registry.

## Graph And Resolution Extensibility

The graph layer should be one of the primary consumers of semantic extensions.

### Required behavior

- extract references based on semantic registry rules
- extract key definitions based on semantic registry rules
- recognize scheme-binding constructs where declared
- preserve raw facts even for custom semantics
- resolve only according to declared semantics, never by guessing

### Required addition to the graph architecture

The [Graph Resolution Architecture](../content-model/graph-and-resolution.md) should be read as requiring one more conceptual input:

- a `semantic-registry` artifact loaded from the tenant bundle

That registry should influence:

- `graph_edges.edge_type`
- map context construction
- key definition derivation
- resolution behavior
- impact analysis classification

## Publishing Extensibility

Publishing is where extension turns from metadata into executable risk.

Rules:

- publish plug-ins and customization code must be bundle-scoped
- publishing must run only in isolated runtimes
- every release must pin bundle version plus publishing profile
- publish diagnostics must include the bundle and semantic registry versions used

This keeps semantic extension reproducible rather than “whatever was installed on the server that day.”

## Editor Extensibility

The BYOE promise means editor support must be an adapter, not the source of truth.

### Oxygen

Oxygen framework packs should be generated or packaged from the same tenant bundle family:

- frameworks
- catalogs
- templates
- CSS
- actions
- validation scenarios

Important rule:

- Oxygen integration may expose semantics nicely
- but it must not be the place where semantics are first defined

That definition belongs in the bundle and semantic registry.

## API Extensibility

If external tools integrate with ForgeDITA, they need to discover capabilities safely.

### Required patterns

- versioned REST endpoints
- capability metadata endpoint
- bundle metadata retrieval
- semantic registry retrieval for authorized clients
- stable diagnostic codes

### Suggested API additions

- `GET /api/v1/tenants/{tenantId}/toolchains/{toolchainBundleId}/artifacts`
- `GET /api/v1/tenants/{tenantId}/toolchains/{toolchainBundleId}/semantic-registry`
- `GET /api/v1/capabilities`

This lets integrations reason about the system without scraping behavior from errors.

## Governance Rules

To keep extension from becoming chaos, ForgeDITA needs governance.

### Rule 1

No platform-visible semantic extension is allowed unless it is declared in the tenant bundle.

### Rule 2

No service may invent extension meaning privately.

Validation, graph, preview, publish, and editor integration must all resolve extension semantics from shared bundle artifacts.

### Rule 3

Extension must be versioned and pinned.

No mutable live-server customization is allowed in the production path.

### Rule 4

Conformance claims must remain bounded.

If a tenant uses an extension that exceeds the public conformance statement, the product must still preserve native DITA and process the tenant bundle correctly, but public compliance claims must remain tied to the published statement.

## Success Criteria

ForgeDITA will have a solid extensibility architecture when:

- a tenant can introduce specialized constructs without custom server patches
- validation, graph extraction, resolution, preview, and publish all interpret those constructs consistently
- all extension behavior is tied to a bundle version
- another tenant can use different extensions without interference
- exports remain native and intelligible without proprietary transformation

That is the standard we should hold the platform to.

Anything less is configurability, not extensibility.
