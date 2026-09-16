# Semantic Registry Model

> **Document status:** Target semantic contract. Current claims are limited to the [Public Conformance Statement](../conformance/public-conformance-statement.md).

Audience: ForgeDITA implementers, tenant toolchain authors, DITA architects, Oxygen/BYOE integration authors, validation and graph service maintainers.

## Purpose

The semantic registry is the missing link between DITA syntax extensibility and CCMS runtime behavior.

DITA grammars can prove that specialized XML is allowed. They do not, by themselves, tell a multi-tenant CCMS how each specialized construct should behave in graph extraction, key resolution, validation orchestration, search, workflow policy, editor assistance, preview, publish, or impact analysis.

ForgeDITA uses a tenant-scoped, versioned semantic registry to declare that meaning explicitly.

## Core Rule

No ForgeDITA service may privately invent tenant-specific DITA semantics.

If a tenant extension should affect platform behavior, that meaning must come from:

- DITA itself, through the built-in base DITA semantic registry
- a tenant semantic registry artifact in an immutable toolchain bundle
- a future approved platform registry extension with explicit versioning

This keeps multitenancy clean. One tenant can extend DITA without leaking behavior into another tenant.

## Non-Goals

The semantic registry is not:

- a replacement for DTD, XSD, RNG, catalog, Schematron, or subject scheme assets
- a proprietary document model
- a reason to modify valid DITA content
- a place to hide tenant-specific code
- an editor-only configuration file
- a publish plugin manifest

It declares cross-service meaning. Other assets still enforce syntax, controlled values, presentation, and executable transforms.

## Relationship To DITA Compliance

ForgeDITA must remain DITA-compliant.

The registry may describe how valid DITA and valid DITA specializations should be interpreted by the CCMS. It must not require proprietary markup or make non-DITA constructs appear DITA-compliant.

Compliance boundary:

- If content is invalid DITA, the registry cannot make it valid.
- If content is valid specialized DITA, the registry can tell ForgeDITA how to treat the specialized constructs.
- If a tenant wants a new attribute to behave like a reference, the grammar/specialization must allow it and the registry must declare its semantics.
- Subject schemes may constrain values and add contextual meaning, but they must not be the only source of new reference semantics.

## Registry Ownership

The registry is owned by the tenant toolchain bundle.

```text
tenant
  toolchain bundle version
    manifest
    semantic registry
    catalogs
    grammars
    Schematron
    subject schemes
    validation config
    editor framework metadata
    publish plugins
```

The registry must be immutable once the bundle is approved or used for a release.

## Registry Identity

Required identity fields:

- `apiVersion`: registry schema/API version, for example `forgedita/v1`
- `kind`: always `SemanticRegistry`
- `metadata.tenant`: owning tenant ID
- `metadata.bundle`: owning toolchain bundle ID
- `metadata.version`: registry version
- `metadata.ditaSpecVersion`: DITA spec version interpreted by this registry
- `metadata.compatibility`: compatibility declaration

Example:

```json
{
  "apiVersion": "forgedita/v1",
  "kind": "SemanticRegistry",
  "metadata": {
    "tenant": "tenant-acme",
    "bundle": "toolchain-acme-core",
    "version": "2026.04.1",
    "ditaSpecVersion": "1.3",
    "compatibility": {
      "extends": ["base-dita-1.3"],
      "breakingChange": false
    }
  },
  "spec": {}
}
```

## Built-In Base Registry

ForgeDITA should treat base DITA semantics as a built-in registry.

The base registry describes platform semantics for standard DITA constructs such as:

- `topicref/@href`
- `xref/@href`
- `image/@href`
- `object/@data`
- `topicref/@keys`
- `@keyref`
- `@conref`
- `@conkeyref`
- subject scheme constructs
- map hierarchy constructs
- processing-role behavior where relevant to graph/search/publish views

Tenant registries extend this base. They do not copy and fork it unless a future compatibility model explicitly permits an override.

## Registry File Format

The canonical registry format is JSON. YAML may be supported as an authoring format if converted to canonical JSON during bundle packaging.

Canonical structure:

```json
{
  "apiVersion": "forgedita/v1",
  "kind": "SemanticRegistry",
  "metadata": {},
  "spec": {
    "namespaces": [],
    "roots": {},
    "semanticRoles": [],
    "references": [],
    "keyDefinitions": [],
    "contentReuse": [],
    "subjectSchemeBindings": [],
    "profiles": [],
    "validationHints": [],
    "editorHints": [],
    "searchHints": [],
    "publishHints": [],
    "workflowHints": []
  }
}
```

## Matching Model

Every semantic rule must have an explicit match block.

Supported match fields:

- `element`: local element name
- `attribute`: local attribute name
- `namespaceUri`: optional namespace URI
- `classContains`: DITA `@class` ancestry token, preferred for specialization-aware matching
- `root`: root element constraint
- `context`: limited XPath or path expression for contextual rules

Example:

```json
{
  "match": {
    "classContains": "map/topicref",
    "attribute": "href"
  },
  "semanticRole": "reference.direct-topic",
  "referenceKind": "href",
  "participatesInGraph": true,
  "requiresMapContext": false
}
```

### Matching Priority

When multiple rules match, ForgeDITA should apply the most specific rule.

Recommended order:

1. exact `classContains` plus attribute plus context
2. exact element plus attribute plus context
3. exact `classContains` plus attribute
4. exact element plus attribute
5. attribute-only base rule
6. root-level default rule

If two tenant rules have the same specificity and conflict, bundle validation must fail.

## Semantic Roles

Semantic roles are stable normalized meanings used across services.

Examples:

- `reference.direct-topic`
- `reference.cross-reference`
- `reference.image`
- `reference.content-reuse`
- `reference.key`
- `reference.content-key`
- `definition.key`
- `definition.subject-scheme`
- `binding.subject-scheme`
- `navigation.map-entry`
- `metadata.taxonomy`
- `policy.release-safety`
- `editor.author-action`

Rules:

- Role names are stable API values.
- Tenant-specific roles must be namespaced, for example `tenant-acme.policy.safety-critical`.
- Platform services may only act on roles they understand.
- Unknown roles must be preserved as metadata and ignored safely.

## Reference Semantics

Reference rules declare how values should enter the graph and resolution layer.

Example:

```json
{
  "match": {
    "classContains": "topic/xref",
    "attribute": "href"
  },
  "semanticRole": "reference.cross-reference",
  "referenceKind": "xref",
  "valueType": "uri",
  "participatesInGraph": true,
  "requiresMapContext": false,
  "impactClass": "content-dependency"
}
```

Required fields:

- `match`
- `semanticRole`
- `referenceKind`
- `valueType`
- `participatesInGraph`
- `requiresMapContext`

Recommended fields:

- `impactClass`
- `allowedScopes`
- `allowedFormats`
- `externalAllowed`
- `resolutionPolicy`
- `editorHint`

## Key Definition Semantics

Key definition rules tell ForgeDITA how key spaces are built.

Example:

```json
{
  "match": {
    "classContains": "map/topicref",
    "attribute": "keys"
  },
  "semanticRole": "definition.key",
  "definitionKind": "keys",
  "valueType": "space-separated-key-list",
  "participatesInMapContext": true
}
```

The map context builder must use these rules to derive key definitions. It must not assume only literal `@keys` forever once tenant extensions are allowed.

## Content Reuse Semantics

Content reuse rules describe `conref`, `conkeyref`, and tenant equivalents.

Example:

```json
{
  "match": {
    "attribute": "conkeyref"
  },
  "semanticRole": "reference.content-key",
  "referenceKind": "conkeyref",
  "valueType": "key-fragment",
  "participatesInGraph": true,
  "requiresMapContext": true,
  "impactClass": "content-reuse"
}
```

Graph extraction should preserve raw facts. Resolution may require map context.

## Subject Scheme Coordination

Subject schemes provide controlled values and contextual classification.

Registry rules can declare:

- which constructs bind subject schemes
- which attributes are controlled by subject scheme values
- how subject-scheme classifications should appear in search facets
- which validation rules require a map context

Example:

```json
{
  "match": {
    "element": "subjectScheme"
  },
  "semanticRole": "definition.subject-scheme",
  "bindsSubjectScheme": true
}
```

Important boundary:

- Subject schemes can constrain a custom attribute.
- The registry declares whether that custom attribute is a reference, metadata facet, release policy input, or editor hint.

## Validation Coordination

Validation consumes the registry alongside grammar, catalog, Schematron, subject scheme, and validation config artifacts.

Validation uses the registry to:

- identify expected root semantics
- choose bundle-specific validation assets
- classify diagnostics by semantic role
- verify registry sanity
- support context-aware checks
- produce editor marker hints

Validation must not use a different semantic interpretation than graph extraction.

## Graph Extraction Coordination

Graph extraction uses the registry to:

- decide which elements/attributes produce graph edges
- classify edge types
- decide whether map context is required
- extract key definitions
- identify subject scheme bindings
- classify impact analysis dependencies
- pin the semantic registry version on extraction records

Rule:

- A graph edge must be traceable to a semantic registry rule or a built-in base DITA registry rule.

## Resolution Coordination

Resolution uses the registry to:

- decide whether a raw edge is direct, key-based, content reuse, taxonomy, or external
- select map-context-dependent resolution paths
- classify ambiguity and failure modes
- preserve semantic role on resolution results
- pin the registry version used for resolution

Resolution must never guess that a custom tenant attribute is key-like just because its name resembles `keyref`.

## Search Coordination

Search uses the registry to:

- identify searchable metadata attributes
- classify facets
- distinguish reference kinds
- index tenant-specific taxonomy fields
- expose safe, tenant-scoped search filters

Example:

```json
{
  "match": {
    "attribute": "auditClass"
  },
  "semanticRole": "metadata.taxonomy",
  "searchFacet": {
    "name": "auditClass",
    "type": "keyword",
    "source": "subject-scheme"
  }
}
```

## Editor Coordination

Editor integrations consume the registry through framework metadata and toolchain artifacts.

The registry can declare:

- author action hints
- controlled attribute hints
- key reference completion behavior
- context requirements
- validation scenario hints
- framework asset relationships

Editor rule:

- Oxygen may present registry semantics nicely, but Oxygen must not be where those semantics are first defined.

## Publishing Coordination

Publish and preview runtimes use the registry to:

- record semantic registry version in runtime manifests
- choose bundle-compatible plugins and profiles
- classify publish diagnostics
- enforce publish policy inputs
- reproduce release behavior later

Release artifacts must pin:

- content baseline
- toolchain bundle
- publish profile
- semantic registry version
- validation config version
- relevant graph/resolution versions

## Workflow And Policy Coordination

The registry may expose metadata that workflow/release gates use, but it should not become a workflow engine.

Example:

```json
{
  "match": {
    "attribute": "auditClass"
  },
  "semanticRole": "policy.release-safety",
  "workflowHint": {
    "releaseGateInput": true,
    "externalEvidenceRecommended": true
  }
}
```

Workflow gates should read these declarations as inputs. Actual policy toggles remain tenant policy.

## Compatibility And Versioning

Registry versions must follow explicit compatibility rules.

Compatible changes:

- adding a new semantic rule for a newly introduced specialization
- adding editor hints that do not change graph/validation/publish behavior
- adding search facets that do not reinterpret existing content
- adding a new namespaced tenant role

Potentially breaking changes:

- changing a reference kind
- changing whether a construct participates in graph extraction
- changing whether map context is required
- changing key definition behavior
- changing subject scheme bindings
- changing release-impact classification
- removing or renaming a role used by stored graph/resolution data

If a registry has breaking changes, ForgeDITA must:

- require a new bundle version
- re-run graph extraction for affected revisions when used in active views
- avoid silently reinterpreting existing baselines/releases
- preserve old runtime manifests and provenance

## Migration Strategy

Registry migration should be explicit and auditable.

Recommended migration flow:

1. Create new toolchain bundle version.
2. Add new semantic registry version.
3. Validate registry against schema and compatibility rules.
4. Validate representative tenant content fixtures.
5. Rebuild graph/map contexts in a staging workspace or background job.
6. Compare impact analysis deltas.
7. Promote bundle if changes are expected and accepted.
8. Keep old releases pinned to old registry versions.

## Bundle Validation Rules

Toolchain bundle validation must check:

- registry JSON is valid
- required metadata exists
- referenced grammar/catalog/Schematron assets exist
- semantic roles are valid or namespaced
- no conflicting rules have equal specificity
- no custom semantics target constructs disallowed by grammar/specialization
- subject scheme references resolve
- editor/framework hints reference existing assets
- publish/profile hints reference existing profiles

Bundle validation failure must block promotion to approved/releasable status.

## API Surface

Existing API surface:

- `GET /api/v1/tenants/{tenantId}/toolchains/{toolchainBundleId}/artifacts`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/editor/bootstrap`
- `GET /api/v1/capabilities`

Recommended future API surface:

- `GET /api/v1/tenants/{tenantId}/toolchains/{toolchainBundleId}/semantic-registry`
- `POST /api/v1/tenants/{tenantId}/toolchains/{toolchainBundleId}/semantic-registry/validate`
- `GET /api/v1/tenants/{tenantId}/toolchains/{toolchainBundleId}/semantic-registry/diff?from={version}`
- `POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/semantic-registry/impact`

All endpoints must enforce tenant RBAC and must never leak another tenant registry.

## Runtime Persistence

Records that depend on semantic interpretation should pin registry identity.

Already aligned:

- graph extraction records include semantic registry version
- map context records include semantic registry version
- runtime manifests include semantic registry version

Future additions:

- persisted resolution results should include semantic registry version
- release metadata should persist semantic registry version directly
- search index records should include semantic registry/indexer version
- validation reports should include semantic registry version

## Example Tenant Extension

Tenant goal:

- Add a specialized topic reference that marks safety-critical procedure dependencies.

Illustrative DITA:

```xml
<acmeProcedureRef href="install-router.dita" auditClass="safety-critical"/>
```

Registry declaration:

```json
{
  "spec": {
    "references": [
      {
        "match": {
          "classContains": "map/topicref",
          "element": "acmeProcedureRef",
          "attribute": "href"
        },
        "semanticRole": "reference.direct-topic",
        "referenceKind": "topicref",
        "valueType": "uri",
        "participatesInGraph": true,
        "requiresMapContext": false,
        "impactClass": "safety-critical-dependency"
      }
    ],
    "searchHints": [
      {
        "match": {
          "attribute": "auditClass"
        },
        "semanticRole": "metadata.taxonomy",
        "searchFacet": {
          "name": "auditClass",
          "type": "keyword",
          "source": "subject-scheme"
        }
      }
    ],
    "workflowHints": [
      {
        "match": {
          "attribute": "auditClass"
        },
        "semanticRole": "policy.release-safety",
        "releaseGateInput": true
      }
    ]
  }
}
```

Expected platform effects:

- graph extraction records a topic dependency
- impact analysis classifies it as safety-critical
- search can facet by `auditClass`
- validation can check controlled values through subject schemes
- release gates can inspect the declared policy input
- Oxygen can offer attribute assistance through framework metadata

## Failure Modes

ForgeDITA must fail safely when registry interpretation is unclear.

Examples:

- Unknown registry schema version: reject bundle promotion.
- Missing registry for tenant extension: accept syntactically valid content, but do not invent custom graph semantics.
- Conflicting semantic rules: reject bundle promotion.
- Missing subject scheme referenced by registry: reject bundle promotion or mark feature unavailable.
- Graph extraction cannot load registry: extraction fails with diagnostic; existing graph facts remain pinned to prior versions.
- Publish runtime registry mismatch: publish job fails before producing releasable artifacts.
