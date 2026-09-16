# ForgeDITA Architecture Brief

> **Document status:** Target architecture and design rationale. This document is not a statement that every described behavior is currently implemented. See [Product Status](../../STATUS.md) and the [Public Conformance Statement](../conformance/public-conformance-statement.md).

## A Standards-First Operating Model for Maintainable DITA Infrastructure

## 1. Why This Exists

Many DITA environments become operationally fragile over time.

The pattern is familiar to experienced practitioners:

- publishing pipelines drift
- validation behavior becomes inconsistent
- customization accumulates across disconnected layers
- semantics become fragmented between editors, transforms, and operational convention
- workflow overhead expands faster than actual content work
- and tribal knowledge becomes required just to keep the system functioning

Teams adapt.

They build spreadsheets.
They memorize process choreography.
They create workarounds for fragile publishing behavior.
They normalize operational friction because the alternative feels unrealistic.

Eventually the system becomes difficult to evolve, difficult to trust, and difficult to leave.

In many organizations, a content change may take minutes while approvals, coordination, publishing verification, and operational tracking consume days.

This document is an attempt to define a cleaner operating model for DITA-based infrastructure.

Not just a better interface.
Not just another CCMS.

A more maintainable architectural foundation.

## 2. The Problem with “DITA Support”

“DITA support” has become an overloaded and often misleading claim.

In practice, it frequently means:

- partial XML handling
- constrained authoring models
- editor-defined behavior
- opaque publishing pipelines
- mutable runtime environments
- platform-shaped workflows
- content that is technically exportable but operationally trapped

The result is often a system where the platform slowly dictates the workflow instead of supporting it.

For organizations operating at scale, this creates real operational risk:

### Loss of Semantic Integrity

DITA semantics become scattered across:

- editor configuration
- Schematron layers
- transforms
- plugins
- undocumented operational behavior

No single source of truth exists.

### Publishing Fragility

Publishing environments drift over time.

Outputs become dependent on:

- mutable plugin stacks
- shared DITA-OT environments
- undocumented configuration behavior
- operational tribal knowledge

Teams stop trusting whether outputs can be reproduced consistently.

### Operational Drag

Complex administration, disconnected systems, approval choreography, and hidden dependencies gradually consume more energy than the documentation itself.

### Vendor Dependency

Migration becomes expensive because the platform has absorbed:

- semantics
- workflow assumptions
- publishing behavior
- and operational knowledge

The content may still be XML.
The operational environment often is not portable.

## 3. ForgeDITA Core Position

ForgeDITA is built around a direct premise:
**Documentation infrastructure should remain understandable and maintainable over time.**

The architecture is intentionally opinionated around:

- explicit behavior
- standards integrity
- reproducibility
- operational clarity
- and long-term maintainability

The goal is not feature accumulation.

The goal is to prevent operational entropy from becoming the defining characteristic of the platform.

## 4. Core Architectural Model

### 4.1 Native XML as the System of Record

DITA content remains:

- unmodified
- fully exportable
- standards-compliant
- free of proprietary representation layers

ForgeDITA does not reinterpret or reshape content to satisfy internal storage assumptions or UI constraints.

This matters because portability is not just a migration feature.

It is operational leverage.

Organizations should be able to:

- inspect their content directly
- preserve standards integrity
- move between environments
- and maintain ownership of their publishing pipeline

without reverse-engineering platform behavior.

### 4.2 Tenant-Scoped Semantic Registry

ForgeDITA centralizes DITA behavior into a declarative semantic registry.

This registry defines:

- specialization handling
- attribute semantics
- metadata interpretation
- subject scheme integration
- validation expectations
- processing behavior
- publishing assumptions

No service is allowed to privately interpret DITA structures outside this registry.

### Why This Matters

In many systems, semantics become fragmented across:

- editor configuration
- transforms
- validation layers
- publishing plugins
- operational convention

Behavior becomes difficult to reason about because the system no longer has a single explicit semantic model.

ForgeDITA attempts to reverse that pattern.

### Example

A tenant may define a specialized hazard domain with tenant-specific expectations for:

- severity handling
- localization behavior
- metadata inheritance
- validation constraints
- and publishing treatment

In many environments, those rules become scattered across:

- Schematron
- editor extensions
- transform customization
- stylesheet logic
- and operational documentation

ForgeDITA attempts to centralize those semantics into a single explicit registry.

### Key Properties

#### Tenant-Scoped

- no cross-tenant semantic leakage
- no shared hidden defaults
- no platform-level mutation of tenant behavior

#### Explicit

- no hidden editor semantics
- no undocumented processing assumptions
- no implicit platform reinterpretation

#### Versioned

- semantic evolution becomes trackable
- releases reference exact semantic states
- historical behavior remains explainable

### 4.3 Graph-Aware Core Services

ForgeDITA treats DITA as a graph, not a collection of files.

Core services operate with awareness of:

- keys and key scopes
- conrefs and reuse chains
- maps and hierarchy
- subject schemes
- metadata relationships
- baseline relationships

This supports:

- reliable validation
- accurate impact analysis
- deterministic publishing inputs
- and more explainable system behavior

Many operational problems in structured content systems emerge when graph relationships are only partially modeled or inconsistently interpreted.

ForgeDITA attempts to make graph behavior explicit throughout the platform.

### 4.4 Bring Your Own Editor

ForgeDITA treats editors as clients, not control points.

- Oxygen is a first-class client
- all operations occur through exposed APIs
- editor behavior does not define platform semantics

This separation matters.

In many environments, operational logic slowly migrates into editor-specific integrations.

Over time this creates:

- hidden coupling
- inconsistent behavior
- fragmented validation logic
- and platform dependence on specific authoring tools

ForgeDITA attempts to keep:

- semantics
- processing
- validation
- and publishing

inside the platform rather than inside the editor.

### 4.5 Immutable Publishing Model

ForgeDITA treats publishing as a reproducible system event.

Every release is pinned to:

- content baseline
- semantic registry version
- toolchain definition
- publishing profile
- validation state

Outputs should be:

- rebuildable
- explainable
- auditable
- and operationally trustworthy

This model exists because many publishing environments slowly become dependent on:

- mutable plugin stacks
- undocumented runtime changes
- environment drift
- and operational memory

Teams often stop trusting whether historical outputs can actually be reproduced.

ForgeDITA attempts to restore determinism.

## 5. Why Existing Systems Become Operationally Fragile

Most documentation systems do not become difficult overnight.

Operational fragility accumulates gradually through:

- mutable publishing environments
- scattered semantics
- layered customization
- undocumented workflow behavior
- editor-defined logic
- hidden dependencies
- and process sediment added over years of operational adaptation

Eventually organizations compensate through:

- spreadsheets
- coordination rituals
- manual verification
- approval choreography
- and institutional tribal knowledge

The system remains functional.

But maintainability declines.

ForgeDITA is designed as an explicit response to that long-term entropy.

## 6. Architectural Trade-Offs

This model intentionally introduces constraints.

Those constraints are not accidental.

They are attempts to replace hidden complexity with explicit structure.

ForgeDITA likely requires:

- stricter semantic governance
- more disciplined versioning
- more explicit lifecycle management
- stronger operational consistency
- clearer publishing definitions
- and greater up-front architectural discipline

The system may become less tolerant of:

- ad hoc customization
- undocumented workflow behavior
- mutable runtime changes
- and implicit operational assumptions

Those trade-offs are intentional.

The architecture favors:

- explainability
- reproducibility
- portability
- and long-term maintainability

over short-term operational convenience.

## 7. Open Questions

Several important questions remain intentionally unresolved.

## Semantic Modeling

Can the semantic registry fully capture complex specialization behavior without introducing excessive rigidity?

## Operational Usability

Where are implicit behaviors necessary for practical authoring experience?

## Performance

What are the performance implications of fully graph-aware processing at enterprise scale?

## Governance

What governance models are required for semantic evolution across long-lived environments?

## Operational Discipline

How much explicitness can organizations realistically sustain without introducing excessive overhead?

These questions matter.

ForgeDITA is architecture-first specifically because these assumptions should be validated before large-scale implementation.

## 8. Intent

This document is not a product pitch.

It is an attempt to define a cleaner operating model for DITA infrastructure.

The goals are intentionally direct:

- preserve the standard
- make behavior explicit
- reduce operational fragility
- support reproducible publishing
- and build systems that remain maintainable over time

## 9. Request for Feedback

If you have experience operating DITA environments at scale, your perspective would be valuable.

Particularly:

- Where does this model break?
- Which assumptions feel unrealistic?
- What operational realities are missing?
- What trade-offs are unacceptable in practice?
- What hidden failure modes should be considered?

## Related

- Website: <https://forgedita.com>
- Contact: <hello@forgedita.com>

ForgeDITA is currently in architecture-first development toward MVP.

Structured content infrastructure designed for long-term maintainability.

---

Copyright 2026 ForgeDITA.
