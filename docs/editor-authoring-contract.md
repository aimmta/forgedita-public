# Editor Authoring Contract

> **Document status:** Normative target contract for ForgeDITA authoring clients. Endpoint availability remains subject to [Product Status](../STATUS.md) and capability discovery.

Contract version: `0.2`

Audience: Oxygen add-on, Oxygen framework pack, and any Bring Your Own Editor client that wants a native-feeling ForgeDITA authoring experience without becoming the system of record.

## Purpose

ForgeDITA editor integrations must be thin clients over open CCMS APIs. Oxygen can receive first-class treatment, but it must not receive private semantics that other editors cannot reproduce.

This contract defines the expected authoring flow, API profile, error handling, and integration responsibilities for editor clients. It is implementation-oriented and intentionally conservative: anything not listed here is not part of the required editor integration contract.

## First Principles

- Native XML remains the authored object. The editor must not add proprietary wrapper markup to make ForgeDITA features work.
- The CCMS owns tenancy, repository state, locks, revisions, validation, graph state, search, workflow metadata, baselines, and publishing jobs.
- Editor behavior must be discovered from the API, toolchain bundle, semantic registry, and framework assets rather than hardcoded into Oxygen-only code.
- Any Oxygen-specific convenience must degrade to the same REST calls for other editors.
- The editor must treat server responses as authoritative, especially for locks, validation diagnostics, graph context, workflow state, release eligibility, and publish status.
- The integration must fail closed on authorization, lock, validation, import, and publish errors.

## Authentication And Scope

Prototype headers:

```http
Authorization: Bearer dev-alex-token
```

or:

```http
X-Principal-Id: principal-alex
```

Production expectation:

- OAuth/OIDC or SAML-backed login resolves a ForgeDITA principal.
- The editor never selects tenant/workspace/repository access on its own.
- The backend resolves tenant/workspace scope from the URL and filters results through RBAC.
- Editors must not cache authorization decisions beyond the current session.

## Bootstrap

The editor starts with one workspace bootstrap call.

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/editor/bootstrap
```

Minimum successful response shape:

```json
{
  "editorContractVersion": "0.2",
  "tenant": {
    "id": "tenant-acme",
    "slug": "acme",
    "name": "Acme Documentation"
  },
  "workspace": {
    "id": "workspace-devices",
    "slug": "devices",
    "name": "Devices"
  },
  "principal": {
    "id": "principal-alex",
    "displayName": "Alex Author",
    "principalType": "user"
  },
  "repositories": [
    {
      "id": "repo-user-guides",
      "slug": "user-guides",
      "name": "User Guides",
      "status": "active",
      "links": {
        "tree": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/repositories/repo-user-guides/tree",
        "export": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/repositories/repo-user-guides/export",
        "import": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/repositories/repo-user-guides/import",
        "looseDitaZipImport": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/repositories/repo-user-guides/import/loose-dita-zip"
      }
    }
  ],
  "toolchains": [
    {
      "id": "toolchain-acme-core",
      "name": "acme-core",
      "version": "2026.04.1",
      "ditaSpecVersion": "1.3",
      "ditaOtVersion": "4.4",
      "status": "approved",
      "publishProfiles": [
        {
          "id": "profile-html5-default",
          "name": "html5-default",
          "outputType": "html5",
          "transtype": "html5"
        }
      ]
    }
  ],
  "importTypes": [
    {
      "id": "loose-dita-zip",
      "requiresForgeDitaManifest": false,
      "supportsDryRun": true,
      "supportsRootMapSelection": true
    }
  ],
  "policies": [
    {
      "policyKey": "release.requireReleaseEligible",
      "enabled": false
    }
  ],
  "editorActions": [
    "editor.workbench",
    "repository.browse",
    "node.checkout",
    "node.working-copy.save",
    "node.checkin",
    "validation.content",
    "search.workspace",
    "search.status",
    "map-context.create",
    "preview.submit",
    "publish.submit"
  ],
  "editorActionCatalog": [
    {
      "id": "node.checkout",
      "label": "Check Out",
      "category": "authoring",
      "method": "POST",
      "hrefTemplate": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/nodes/{nodeId}/checkout",
      "requiredPermission": "node.write",
      "target": "node"
    }
  ],
  "frameworks": [
    {
      "frameworkId": "framework-toolchain-acme-core",
      "source": "toolchain-bundle",
      "toolchainBundleId": "toolchain-acme-core",
      "semanticRegistryVersion": "2026.04.1",
      "assets": {
        "catalogs": [],
        "grammars": [],
        "schematron": [],
        "semanticRegistries": [],
        "validationConfigs": [],
        "css": [],
        "templates": [],
        "authorActions": []
      }
    }
  ]
}
```

Editor behavior:

- If bootstrap returns `403`, show a login/access problem and do not continue.
- If bootstrap returns no repositories, show an empty authorized workspace, not a connection failure.
- If bootstrap returns no toolchains, disable validation, preview, and publish actions that require a bundle.
- If the editor contract version is unsupported, warn and continue only with explicitly compatible actions.
- Prefer `editorActionCatalog` for labels, categories, endpoint templates, and required permissions instead of hardcoding action metadata in an Oxygen plugin.

## Workbench

Editors that need a browse/search/action launch packet should call the workbench endpoint after bootstrap.

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/editor/workbench
```

Optional fast-start query:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/editor/workbench?includeTrees=false
```

Minimum workbench response shape:

```json
{
  "editorContractVersion": "0.2",
  "scope": {
    "tenant": { "id": "tenant-acme" },
    "workspace": { "id": "workspace-devices" },
    "principal": { "id": "principal-alex" }
  },
  "navigation": {
    "repositories": [
      {
        "id": "repo-user-guides",
        "name": "User Guides",
        "tree": { "path": "/", "children": [] },
        "search": {
          "href": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/search?repositoryId=repo-user-guides&q={query}&limit={limit}&offset={offset}",
          "statusHref": "/api/v1/tenants/tenant-acme/workspaces/workspace-devices/search/status?repositoryId=repo-user-guides"
        }
      }
    ]
  },
  "search": {
    "filters": ["repositoryId", "baselineId", "releaseId", "nodeType", "mediaType", "keyName", "edgeType"],
    "resultFeatures": ["snippets", "highlights", "dita-facets", "graph-facets"]
  },
  "actionGroups": [
    {
      "category": "authoring",
      "actions": []
    }
  ],
  "taskFlows": [
    {
      "id": "checkout-edit-checkin",
      "actions": ["node.checkout", "node.working-copy.save", "validation.content", "node.checkin"]
    }
  ]
}
```

Editor behavior:

- Use workbench navigation to populate repository browse/search views.
- Use `actionGroups` for menus/toolbars instead of hardcoded Oxygen action lists.
- Use `taskFlows` as recommended workflow recipes; each listed action still maps to a normal REST endpoint.
- Use `includeTrees=false` when the editor wants faster startup and will lazy-load repository trees itself.

## Browse And Open

Repository list:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/repositories
```

Repository tree:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/repositories/{repositoryId}/tree
```

Node metadata:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}
```

Node content:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/content
```

Editor behavior:

- Use repository tree responses for browse views.
- Use node metadata to display lock status, media type, node type, and latest revision.
- Open XML and binary assets from `content` without rewriting payloads.
- Treat `404` as not visible or not found; do not reveal whether a hidden object exists.

## Checkout, Edit, Save, Check In

Checkout:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/checkout
Content-Type: application/json

{
  "lockType": "checkout",
  "leaseMinutes": 60
}
```

Save working copy:

```http
PUT /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/working-copy
Content-Type: application/json

{
  "content": "<task id=\"install-router\"><title>Install the router</title></task>",
  "mediaType": "application/xml"
}
```

Check in:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/checkin
Content-Type: application/json

{
  "commitMessage": "Updated installation prerequisite.",
  "content": "<task id=\"install-router\"><title>Install the router</title></task>",
  "unlock": true,
  "sourceEditor": "oxygen"
}
```

Cancel checkout:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/cancel-checkout
```

Editor behavior:

- Checkout before modifying repository-controlled content.
- Autosave through working-copy save, not check-in.
- Check-in creates a revision and optionally releases the lock.
- Use `sourceEditor` for diagnostics/audit only; it must not change repository semantics.
- On `409 lock-conflict`, switch the document to read-only and show the current lock owner if provided.
- On `422`, keep the working copy open and show validation or policy diagnostics.

## Validation

Validate content:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/validation/content
Content-Type: application/json

{
  "content": "<task id=\"install-router\"><title>Install the router</title></task>",
  "toolchainBundleId": "toolchain-acme-core",
  "mapContextId": "ctx-example",
  "ditavalNodeId": "node-admin-ditaval"
}
```

Expected response behavior:

- Aggregate result is one of `valid`, `warning`, or `invalid`.
- Per-phase diagnostics identify bundle load, well-formedness, DITA-OT, grammar, Schematron, policy, and profile-context results when available.
- Diagnostics must be convertible into editor markers without losing message, severity, phase, and provenance.
- `editorMarkers` provides the stable marker-focused projection for editor gutter/problems integrations.
- `mapContextId` and `ditavalNodeId` are optional. When supplied, validation consumes subject schemes from the map context and DITAVAL rules from the selected node as explicit profile context.

Example marker shape:

```json
{
  "id": "marker-0001",
  "severity": "warning",
  "message": "Task content should include a prereq element.",
  "line": 1,
  "column": 1,
  "endLine": 1,
  "endColumn": 1,
  "phase": "schematron",
  "source": "acme-task-rules",
  "systemId": "acme-task-rules",
  "ruleId": "acme.task.prereq",
  "code": "acme.task.prereq",
  "remediationHint": "Add a prereq element when the task requires prerequisite information."
}
```

Editor behavior:

- Validate current unsaved content before check-in when possible.
- Use bundle/toolchain selection from bootstrap.
- Do not replace DITA-aware editor validation with ForgeDITA validation; use ForgeDITA as the server authority for repository/publish compliance.
- Map `editorMarkers` directly to Oxygen markers/problems when available, and fall back to `diagnostics` only when marker metadata is missing.

## Search

Workspace search:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/search?q=router&limit=20&offset=0
```

Search behavior:

- Results are RBAC-filtered.
- Responses include paging metadata.
- Results include match provenance and first-pass DITA facets for IDs, keys, and references.

Editor behavior:

- Use search for global find/browse, not as the source of repository truth.
- Open selected results through node metadata and content endpoints.
- Preserve paging state in the UI.

## Graph, Map Context, And Impact

Extract graph facts for a node:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/graph-extract
```

Inspect references:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/references/outbound
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/references/inbound
```

Create map context:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{mapNodeId}/map-contexts
```

Resolve a key within a context:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/keys/{keyName}
```

Impact:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/impact
```

Editor behavior:

- Use map contexts for keyref/conkeyref-oriented authoring assistance.
- Use impact before risky edits, deletes, renames, or release work.
- Treat graph extraction as a server refresh operation, not a local-only parser.

## Workflow And Release Eligibility

Read workflow state:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/workflow
```

Update workflow state:

```http
PUT /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/workflow
Content-Type: application/json

{
  "state": "approved",
  "releaseEligible": true,
  "externalSystem": "DOORS NG",
  "externalReference": "DNG-ROUTER-42",
  "externalUrl": "https://doors.example/items/DNG-ROUTER-42",
  "rationale": "External approval evidence captured in DOORS NG."
}
```

Editor behavior:

- Show workflow state as CCMS metadata, not as embedded XML.
- Treat `releaseEligible` as a lightweight publishing gate flag, not a full approval workflow.
- Preserve external evidence links without attempting to replace regulated systems.

## Preview And Publish

Preview:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/preview-jobs
Content-Type: application/json

{
  "targetType": "baseline",
  "targetId": "baseline-router-a",
  "toolchainBundleId": "toolchain-acme-core",
  "profileName": "html5-default"
}
```

Publish:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/publish-jobs
Content-Type: application/json

{
  "targetType": "baseline",
  "targetId": "baseline-router-a",
  "toolchainBundleId": "toolchain-acme-core",
  "profileName": "pdf-regulated",
  "releaseId": "release-router-a"
}
```

Job status:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/jobs/{jobId}
```

Job artifacts:

```http
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/jobs/{jobId}/artifacts
GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/jobs/{jobId}/artifacts/{artifactId}/content
```

Editor behavior:

- Preview and publish actions must use server jobs.
- The editor must not run its own DITA-OT and claim that result as the ForgeDITA release output.
- Release/publish actions must respect tenant policy `release.requireReleaseEligible`.
- Show runtime manifest, log, diagnostics, and output artifacts when available.

## Import

ForgeDITA package import:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/repositories/{repositoryId}/import
```

Loose DITA ZIP import:

```http
POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/repositories/{repositoryId}/import/loose-dita-zip
Content-Type: application/json

{
  "packageBase64": "UEsDB...",
  "pathPrefix": "/imported",
  "dryRun": true
}
```

Editor behavior:

- Always dry-run before writing an imported package when the UI can support it.
- If multiple root maps are detected, ask the user to choose a root map and retry with `rootMapPath`.
- Treat checksum failures as blocking errors.
- Do not require proprietary ForgeDITA metadata for normal DITA ZIP imports.

## Error Contract

ForgeDITA uses problem responses for API failures.

Expected editor handling:

- `400`: malformed request; show client-side correction.
- `403`: authenticated principal lacks permission; disable the attempted action in the current scope.
- `404`: object not found or not visible; refresh browse state.
- `409`: lock conflict or state conflict; show conflict details and protect local edits.
- `422`: valid request rejected by validation, release gate, import preflight, or policy rule; keep user work intact and show diagnostics.
- `500`: server/runtime failure; keep local content and offer retry/report.

## Oxygen Integration Mapping

Oxygen add-on responsibilities:

- login/session setup
- workspace bootstrap
- workbench discovery for browse/search/action launch state
- repository browser view
- open content into Oxygen editor tabs
- checkout/check-in/cancel actions
- working-copy autosave action
- validation action that maps ForgeDITA diagnostics to Oxygen markers
- search view
- reference/impact views
- import wizard
- preview/publish actions
- artifact viewer/download actions

Oxygen framework pack responsibilities:

- catalogs and grammar references from the tenant toolchain bundle
- CSS and author mode presentation
- templates and new-document actions
- specialization-aware author actions declared by the bundle/semantic registry

The add-on may be Oxygen-specific. The semantics it uses must still come from ForgeDITA APIs and toolchain assets.

## Contract Governance

Editor behavior must remain API-first and bundle-derived. Oxygen-specific convenience may improve the experience, but it must not define private semantics unavailable to other editor clients.

Editor framework/action metadata should follow the [Semantic Registry Model](content-model/semantic-registry.md), so Oxygen behavior remains derived from tenant bundle semantics rather than hardcoded editor logic.

Implementation status and public future work are summarized in the repository [Product Status](../STATUS.md) and [Public Roadmap](../ROADMAP.md). Detailed remediation tracking remains internal.
