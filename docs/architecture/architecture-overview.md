# Architecture Overview

ForgeDITA separates content, semantics, relationships, validation, workflow, and publishing into explicit service contracts.

## System View

```mermaid
flowchart TD
  A["Editors and integrations"] --> B["API gateway"]
  B --> C["Content service"]
  B --> D["Graph and resolution"]
  B --> E["Validation"]
  B --> F["Workflow and releases"]
  B --> G["Publish orchestrator"]
  G --> H["Tenant toolchain registry"]
  C --> I["Native XML and assets"]
  D --> J["Context and dependency index"]
  F --> K["Baselines and audit evidence"]
  H --> L["Isolated build runtime"]
```

## Content Service

Stores topics, maps, subject schemes, DITAVAL files, catalogs, grammars, and binary assets without converting DITA into a proprietary document model.

## Graph and Resolution

Extracts declared relationships and evaluates context-sensitive behavior such as key lookup and content reuse. Resolution results belong to a map context and content state, not to a global cache of guesses.

## Validation

Coordinates XML, grammar, Schematron, DITA-aware, and tenant-policy checks. Results identify location, severity, rule source, and completion state.

## Baselines and Releases

Pins exact content revisions and associates them with workflow evidence, toolchain state, and published artifacts.

## Toolchain Registry

Stores immutable tenant-owned bundles containing DITA-OT, plugins, catalogs, grammar shells, validation assets, themes, and publish profiles.

## Publish Orchestration

Combines a baseline, toolchain bundle, and publish profile into an attributable job with logs, diagnostics, artifacts, and a runtime manifest.

## Authoring Clients

Oxygen and other clients consume the same server-authoritative operations for browse, checkout, save, validation, search, impact analysis, workflow, preview, and publish.

## Status Boundary

This document summarizes target architecture. See [Product Status](../../STATUS.md) and the [Public Conformance Statement](../conformance/public-conformance-statement.md) for current claims.
