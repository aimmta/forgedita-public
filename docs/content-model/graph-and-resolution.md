# Graph Resolution Architecture

> **Document status:** Beta architecture aligned with executable graph, map-context, resolution, and impact-analysis paths. Complete DITA relationship semantics remain bounded by the [Public Conformance Statement](../conformance/public-conformance-statement.md).

This document turns the `Graph & Resolution Service` from the [Reference Architecture](../architecture/reference-architecture.md) into an implementation-oriented design for ForgeDITA.

It is intentionally concrete. The goal is to define the rules, persisted data, and execution model well enough that we can build the graph layer without backing into a proprietary or underpowered interpretation of DITA.

## Purpose

The graph/resolution layer is the semantic core of the CCMS.

It is responsible for:

- extracting cross-document relationships from native DITA/XML
- resolving references in the correct map and profile context
- supporting impact analysis, validation, preview, and publish
- preserving reproducibility by pinning graph facts to revisions and baselines

If this layer is wrong, the rest of the system will behave like a generic XML repository instead of a DITA-native CCMS.

## Design Principles

These are non-negotiable for ForgeDITA.

- Native DITA/XML remains the system of record.
- Graph facts are derived from content, not manually authored into a proprietary model.
- Raw references are stored exactly as authored, even when unresolved or invalid.
- Resolution results are context-dependent and must never overwrite the raw extracted facts.
- Revision-scoped graph data is immutable once extracted.
- Baseline and release operations must resolve against pinned revisions, not live head state.
- The graph service must be specialization-aware. It cannot hard-code only base element names and still claim DITA compliance.
- The graph service must consume declared tenant semantics from the toolchain bundle, not infer extension meaning ad hoc.

## Scope

Initial scope:

- `href`
- `conref`
- `conkeyref`
- `keyref`
- `mapref`
- topicref map hierarchy
- key definitions
- subject scheme bindings
- impact analysis
- map-context-aware resolution

Deferred but anticipated:

- branch-aware graph views
- full profile-conditioned graph views
- learning/training domain-specific convenience APIs
- cross-baseline diff optimization

## Branching Boundary

Graph facts are revision-scoped. Branch-aware graph views must be modeled explicitly rather than inferred from live repository head state.

When branch semantics are introduced, they should add an explicit branch dimension to graph views and context snapshots without changing the meaning of immutable revision facts.

## Conceptual Model

There are three different kinds of persisted information, and we must keep them separate.

1. `Document facts`
   What a specific revision literally contains.

2. `Context facts`
   What a map defines around a topic or map, especially keys and subject-scheme bindings.

3. `Resolution results`
   What a given reference resolves to inside a specific context.

This separation is critical.

Example:

- A topic revision may contain `keyref="install-step"`.
- That raw fact is revision-scoped and immutable.
- The actual target of `install-step` depends on the map context.
- The resolution result therefore belongs to a map-context snapshot, not to the topic revision itself.

## Service Responsibilities

The graph/resolution service should own:

- extraction of outbound references from topic/map revisions
- key space construction from maps
- subject-scheme binding discovery
- context-aware resolution of references
- inbound/outbound dependency queries
- impact analysis over revisions, baselines, and releases
- resolution diagnostics for unresolved or ambiguous references
- semantic-registry-driven classification of reference-bearing constructs

It should not own:

- raw XML storage
- publishing
- schema validation
- workflow state

It should consume, but not define:

- tenant semantic registry artifacts from the toolchain registry
- tenant grammar and catalog provenance needed to interpret specialized constructs consistently

## Semantic Registry Input

The graph service must consume a tenant-scoped semantic registry as defined in the [Semantic Registry Model](semantic-registry.md). The [Extensibility Architecture](../architecture/extensibility-architecture.md) provides the broader rationale.

The semantic registry is an input to graph extraction and resolution, not a replacement for DITA grammars.

The graph layer uses it to answer questions grammars alone do not answer cleanly enough for a CCMS runtime:

- which attributes are reference-bearing
- what kind of reference they represent
- whether they define keys
- whether they require map context
- whether they bind subject schemes
- whether a specialized construct should appear in impact analysis

Platform rule:

- base DITA semantics should be treated as the built-in platform semantic registry
- tenant extensions should augment that registry through bundle-scoped artifacts
- no graph code should invent additional semantic meaning privately

## Data Model

The graph layer should reuse `nodes` and `revisions` as canonical content identity.

New tables should be added in the executable runtime schema.

### 1. `graph_extractions`

Tracks extraction status for a specific revision.

Suggested columns:

- `id text primary key`
- `tenant_id text not null`
- `workspace_id text not null`
- `repository_id text not null`
- `node_id text not null references nodes(id)`
- `revision_id text not null references revisions(id)`
- `toolchain_bundle_id text references toolchain_bundles(id)`
- `semantic_registry_version text not null`
- `source_sha256 text not null`
- `extractor_version text not null`
- `status text not null check (status in ('pending', 'succeeded', 'failed'))`
- `error_payload text`
- `created_at timestamptz not null default current_timestamp`
- `updated_at timestamptz not null default current_timestamp`
- `unique (revision_id, extractor_version)`

Purpose:

- lets us re-extract when the extractor changes
- keeps extraction immutable for a revision plus extractor version
- gives us an auditable failure record
- records which semantic registry version was used to interpret the revision

### 2. `graph_edges`

Stores raw outbound reference facts extracted from a revision.

Suggested columns:

- `id text primary key`
- `tenant_id text not null`
- `workspace_id text not null`
- `repository_id text not null`
- `node_id text not null references nodes(id)`
- `revision_id text not null references revisions(id)`
- `edge_type text not null check (edge_type in ('href', 'xref', 'topicref', 'mapref', 'conref', 'conkeyref', 'keyref', 'image', 'subject-scheme-bind', 'schemeref'))`
- `source_element_name text not null`
- `source_element_id text`
- `source_attr_name text not null`
- `source_xpath text not null`
- `source_line integer`
- `source_column integer`
- `raw_value text not null`
- `raw_scope text`
- `raw_format text`
- `raw_type text`
- `target_uri text`
- `target_fragment text`
- `target_key_name text`
- `target_id_ref text`
- `created_at timestamptz not null default current_timestamp`

Recommended indexes:

- `(revision_id)`
- `(node_id, edge_type)`
- `(target_key_name) where target_key_name is not null`
- `(target_uri) where target_uri is not null`

Important rule:

- `graph_edges` stores what was authored, not what it resolved to.

### 3. `map_contexts`

Represents a context root used for key and resolution evaluation.

Suggested columns:

- `id text primary key`
- `tenant_id text not null`
- `workspace_id text not null`
- `repository_id text not null`
- `map_node_id text not null references nodes(id)`
- `map_revision_id text not null references revisions(id)`
- `baseline_id text references baselines(id)`
- `toolchain_bundle_id text references toolchain_bundles(id)`
- `semantic_registry_version text not null`
- `context_hash text not null`
- `status text not null check (status in ('active', 'superseded', 'failed'))`
- `created_at timestamptz not null default current_timestamp`
- `updated_at timestamptz not null default current_timestamp`
- `unique (map_revision_id, baseline_id, context_hash)`

Purpose:

- pins a concrete resolution context
- allows the same map revision to have different context snapshots if later we add profile or branch dimensions
- records which semantic registry version was used to derive keys and context semantics

### 4. `map_context_entries`

Stores the map tree membership for a context.

Suggested columns:

- `context_id text not null references map_contexts(id) on delete cascade`
- `entry_seq bigint not null`
- `parent_entry_seq bigint`
- `topicref_node_id text references nodes(id)`
- `topicref_revision_id text references revisions(id)`
- `href_node_id text references nodes(id)`
- `href_revision_id text references revisions(id)`
- `navtitle text`
- `processing_role text`
- `scope text`
- `format text`
- `collection_type text`
- `keyscope text`
- `is_resource_only boolean not null default false`
- `primary key (context_id, entry_seq)`

Purpose:

- gives us a concrete map tree
- supports nearest-ancestor and keyscope-aware resolution later

### 5. `key_definitions`

Stores key definitions derived from a map context.

Suggested columns:

- `id text primary key`
- `context_id text not null references map_contexts(id) on delete cascade`
- `key_name text not null`
- `keyscope_prefix text`
- `fully_qualified_key text not null`
- `defining_entry_seq bigint not null`
- `target_node_id text references nodes(id)`
- `target_revision_id text references revisions(id)`
- `target_uri text`
- `target_fragment text`
- `target_scope text`
- `target_format text`
- `target_type text`
- `created_at timestamptz not null default current_timestamp`
- `unique (context_id, fully_qualified_key, defining_entry_seq)`

Recommended indexes:

- `(context_id, fully_qualified_key)`
- `(context_id, key_name)`

Purpose:

- makes key lookup explicit and reproducible
- supports both exact fully-qualified keys and local-name lookup

### 6. `subject_scheme_bindings`

Stores subject scheme relationships discovered from maps.

Suggested columns:

- `id text primary key`
- `context_id text not null references map_contexts(id) on delete cascade`
- `binding_node_id text not null references nodes(id)`
- `binding_revision_id text not null references revisions(id)`
- `scheme_node_id text references nodes(id)`
- `scheme_revision_id text references revisions(id)`
- `scheme_uri text`
- `created_at timestamptz not null default current_timestamp`

Purpose:

- allows validation and preview to ask which schemes apply in a given map context

### 7. `resolution_results`

Stores resolved outcomes for raw references inside a specific context.

Suggested columns:

- `id text primary key`
- `tenant_id text not null`
- `workspace_id text not null`
- `context_id text not null references map_contexts(id) on delete cascade`
- `edge_id text not null references graph_edges(id) on delete cascade`
- `resolution_kind text not null check (resolution_kind in ('direct', 'key', 'conref', 'conkeyref', 'mapref', 'subject-scheme', 'external'))`
- `status text not null check (status in ('resolved', 'unresolved', 'ambiguous', 'external'))`
- `resolved_node_id text references nodes(id)`
- `resolved_revision_id text references revisions(id)`
- `resolved_fragment text`
- `resolved_key_definition_id text references key_definitions(id)`
- `error_code text`
- `error_message text`
- `created_at timestamptz not null default current_timestamp`
- `unique (context_id, edge_id)`

Important rule:

- `resolution_results` is always disposable and rebuildable from extracted facts plus context.

## Extraction Rules

Extraction should run against a concrete revision body and produce deterministic results.

In addition to revision content, extraction must also load the effective semantic registry for the tenant and selected toolchain bundle.

### General rules

- Extract from the stored revision body, not from working copies unless explicitly requested.
- Preserve raw attribute values exactly.
- Record source location whenever reasonably possible.
- Do not fail the entire extraction because one reference is malformed.
- Unresolved references are not extraction failures.
- Classify reference semantics from the semantic registry, not only from hard-coded base element names.

### Topic/map references

Extract from:

- `@href`
- `@conref`
- `@conkeyref`
- `@keyref`
- `@keys`
- map relationship elements like `topicref`, `mapref`, `topichead`, `keydef`

Also extract from:

- specialized elements and attributes declared as reference-bearing in the semantic registry
- specialized key-defining constructs declared in the semantic registry
- specialized subject-scheme binding constructs declared in the semantic registry

### Target interpretation

- `href` and `conref` should be split into URI and fragment parts.
- `keyref` and `conkeyref` should preserve the raw key name exactly.
- `keys` should emit one `key_definition` candidate per key token during map-context build, not directly during raw extraction.
- specialized reference-bearing constructs should be classified according to the semantic registry and stored in `graph_edges.edge_type` using a stable normalized kind

## Resolution Rules

Resolution must be map-context-aware.

Resolution must also be semantic-registry-aware.

### Direct URI references

For `href`, `xref`, `topicref`, `mapref`, and `conref`:

- resolve against the source revision’s path base
- normalize the path relative to the repository
- resolve target node by repository path when internal
- mark external URIs as `external`, not failures

For specialized direct references:

- apply the semantic kind declared in the registry
- do not guess direct-reference behavior from attribute names alone

### Key-based references

For `keyref` and `conkeyref`:

- require a `map_context`
- look up the effective key in `key_definitions`
- later support nearest `keyscope`
- if no key is found, mark `unresolved`
- if multiple equally valid keys exist in the same effective scope, mark `ambiguous`

For specialized key-like references:

- resolve them only if the semantic registry declares them as key-based
- otherwise preserve the raw extracted fact and return a structured semantic-classification error

### Conref and conkeyref

Resolution result should point to:

- target topic node/revision
- target fragment or element id

Later implementation can add a second-layer materialization service for resolved content expansion, but the graph layer only needs to resolve identity first.

## Map Context Construction

Map contexts should be built from a map revision, optionally pinned to a baseline.

Map context construction must use the same semantic registry version that graph extraction used for the participating revisions.

Algorithm:

1. Start from a map revision.
2. Walk topicrefs and maprefs in order.
3. Resolve referenced maps/topics by revision identity where possible.
4. Build the ordered `map_context_entries`.
5. Derive key definitions from `@keys`.
6. Derive subject scheme bindings.
7. Persist the context snapshot.
8. Resolve raw graph edges for member topics against that context.

When tenant extensions are present:

5. Derive key definitions from base DITA constructs and registry-declared specialized constructs.
6. Derive subject scheme bindings from base DITA constructs and registry-declared specialized constructs.

Important rule:

- Resolution without an explicit context is allowed only for direct URI references.
- Key-based reference resolution without a map context should return a structured error, not a guessed answer.

## Baselines And Reproducibility

Graph correctness must survive time.

Rules:

- `graph_edges` are tied to `revision_id`
- `map_contexts` must be buildable from pinned `map_revision_id` plus optional `baseline_id`
- `resolution_results` for preview/publish should be computed against the exact baseline in use
- impact analysis on live head and impact analysis on a baseline are different queries and must stay separate
- extraction and context records must also pin the semantic registry version used, so later bundle changes do not silently reinterpret old content

## API Surface

These APIs should be added once the tables exist.

### Extraction and graph status

- `POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/graph-extract`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/graph-status`

### Reference inspection

- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/references/outbound`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/references/inbound`

### Map context

- `POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{mapNodeId}/map-contexts`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/entries`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/keys`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/keys/{keyName}`

### Resolution

- `POST /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/resolutions`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/resolutions`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/map-contexts/{contextId}/resolutions/{edgeId}`

### Impact analysis

- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/nodes/{nodeId}/impact`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/revisions/{revisionId}/impact`
- `GET /api/v1/tenants/{tenantId}/workspaces/{workspaceId}/baselines/{baselineId}/impact`

## Risks To Watch

- confusing raw extracted facts with context-resolved answers
- trying to resolve keyrefs without a map context
- binding graph facts to current head instead of pinned revisions
- over-optimizing before the semantics are right
- accidentally hard-coding base element names in ways that break specialization-awareness later
- letting graph extraction consume a different semantic interpretation model than validation

## Success Criteria

This design will be working when:

- a revision can be extracted into stable raw graph facts
- a map revision can produce a stable key/context snapshot
- the same revision can resolve differently in different valid contexts
- inbound impact analysis can name all known dependents of a revision
- preview and publish can consume pinned context/resolution data instead of ad hoc runtime crawling

That is the point where ForgeDITA starts acting like a true DITA-native CCMS rather than a repository with XML endpoints.
