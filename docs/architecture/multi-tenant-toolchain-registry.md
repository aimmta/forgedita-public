# ForgeDITA Whitepaper

> **Document status:** Target architecture and design rationale. Current product claims are limited to the [Public Conformance Statement](../conformance/public-conformance-statement.md).

## Multi-Tenant Architecture and the Toolchain Registry Model

## 1. Why Multi-Tenancy Usually Fails in Practice

Multi-tenancy in many CCMS environments is treated primarily as a deployment model.

Shared infrastructure is layered with:

- partial tenant isolation
- configuration overlays
- mutable environments
- and operational convention

At small scale this can appear manageable.

Over time it often creates:

- publishing drift
- inconsistent behavior
- hidden coupling
- operational uncertainty
- and tenant-specific workarounds embedded inside shared platform logic

For DITA systems, the problem becomes much more serious.

DITA processing is not just storage and retrieval.

It includes:

- specialization handling
- key resolution
- metadata interpretation
- subject scheme behavior
- validation semantics
- publishing transformations
- and graph-aware processing logic

If these behaviors are not fully isolated and explicitly defined, operational consistency becomes difficult to guarantee.

Eventually teams stop trusting:

- whether environments behave identically
- whether releases are reproducible
- whether validation is consistent
- and whether publishing behavior changed silently over time

ForgeDITA treats this as an architectural problem, not merely an infrastructure problem.

## 2. ForgeDITA Position

Multi-tenancy must extend beyond hosting.

It must include:

- content
- semantics
- graph behavior
- validation
- processing
- publishing
- and operational state

Every layer of the platform must remain:

- tenant-scoped
- explicit
- reproducible
- and explainable

ForgeDITA approaches this through strict architectural boundaries and a central concept:

### The Toolchain Registry

## 3. The Toolchain Registry

The toolchain registry is the authoritative definition of how content is processed for a tenant.

It defines:

- DITA-OT version
- plugin sets
- catalog resolution
- specialization shells and constraints
- Schematron and validation rules
- publishing profiles
- transformation parameters
- semantic registry references
- runtime processing expectations

Each registry entry represents a complete, versioned processing environment.

## 3.1 Why This Exists

Many publishing environments drift slowly over time.

A plugin changes.
A runtime changes.
A transform evolves.
A hidden dependency appears.
A server configuration differs slightly between environments.

Eventually organizations cannot confidently answer:

- Why did this output change?
- Which plugins produced this release?
- Can this build be reproduced?
- Which validation rules were active?
- Did tenant behavior change unexpectedly?

The toolchain registry exists to make processing behavior explicit and reproducible.

## 3.2 Tenant-Scoped

Each tenant has its own registry.

- no shared global defaults
- no hidden inheritance
- no cross-tenant mutation
- no platform-level reinterpretation of tenant behavior

Tenant isolation is treated as an architectural boundary, not a convenience layer.

## 3.3 Versioned

Toolchains are immutable once published.

- changes produce new versions
- historical versions remain available
- releases reference exact toolchain states

This supports:

- reproducibility
- auditability
- explainability
- and safer operational evolution

## 3.4 Explicit

All processing behavior is declared.

- no implicit runtime assumptions
- no undocumented editor behavior
- no hidden platform semantics
- no mutable publishing environments

The goal is operational clarity.

Not operational magic.

## 4. Multi-Tenancy from Foundation to Runtime

ForgeDITA enforces tenant isolation throughout the entire system.

## 4.1 Storage Layer

- content stored per tenant
- no shared content pools
- clean export always available
- native XML preserved as system of record

Portability remains operationally important.

Organizations should not lose ownership of their content because platform assumptions became embedded over time.

## 4.2 Semantic Layer

Each tenant maintains its own semantic registry.

This defines:

- specialization behavior
- metadata interpretation
- processing expectations
- validation assumptions
- and publishing semantics

No service is allowed to privately invent semantics outside the registry.

This attempts to prevent one of the most common long-term failure patterns in DITA environments:
**Scattered semantics across disconnected operational layers**

## 4.3 Graph Layer

All graph operations remain tenant-scoped.

This includes:

- keys
- conrefs
- maps
- metadata relationships
- subject schemes
- baseline relationships

Graph behavior should remain:

- deterministic
- explainable
- and reproducible

Many operational inconsistencies emerge when graph interpretation differs across tools or services.

ForgeDITA attempts to centralize graph understanding.

## 4.4 Validation Layer

Validation rules remain tied to:

- tenant semantics
- toolchain state
- and explicit processing expectations

Client and server validation should remain aligned while still operating independently.

The goal is consistency without hidden coupling.

## 4.5 Toolchain Layer

The toolchain registry defines all processing inputs.

This includes:

- DITA-OT versions
- plugin versions
- runtime configuration
- catalog behavior
- transformation expectations
- validation configuration

Publishing environments should remain inspectable and reproducible.

Not dependent on operational memory.

## 4.6 Publishing Layer

Publishing is treated as a deterministic system event.

Each release references:

- content baseline
- semantic registry version
- toolchain version
- publishing profile
- validation state

Outputs should remain:

- rebuildable
- auditable
- explainable
- and operationally trustworthy

## 4.7 API Layer

All services are tenant-aware by design.

- no hidden tenant routing
- no shared mutable semantics
- no platform-level behavioral ambiguity

Behavior should remain consistent across all clients.

## 5. Why Existing Multi-Tenant Systems Drift

Many systems become operationally fragile because:

- configuration accumulates faster than governance
- mutable environments create publishing uncertainty
- semantics become scattered across tooling layers
- customization mixes with platform logic
- and operational behavior becomes dependent on institutional memory

Organizations compensate through:

- release rituals
- undocumented procedures
- manual verification
- publishing workarounds
- and workflow choreography

The environment may still function.

But predictability gradually declines.

ForgeDITA attempts to reduce that entropy through:

- explicit structure
- immutable processing environments
- versioned semantics
- and architectural isolation

## 6. Architectural Trade-Offs

This model introduces operational discipline intentionally.

It likely requires:

- stronger governance
- more explicit configuration
- stricter version management
- clearer lifecycle control
- and reduced tolerance for undocumented operational behavior

The architecture may increase:

- up-front modeling effort
- semantic governance overhead
- storage duplication
- and infrastructure complexity in some areas

Those constraints are intentional.

ForgeDITA favors:

- explainability
- reproducibility
- portability
- and maintainability

over operational ambiguity.

## 7. Open Questions

Important unresolved questions remain.

## Toolchain Promotion

How should toolchain promotion workflows operate across environments without reintroducing drift?

## Semantic Governance

What governance structures are required for long-lived semantic evolution?

## Performance

How can graph-aware isolation remain performant at enterprise scale?

## Operational Experience

How much explicitness can organizations realistically sustain before usability suffers?

## Adoption Path

How can existing environments transition toward explicit operational models without excessive migration burden?

These are practical concerns, not theoretical ones.

ForgeDITA should only succeed if the operational model remains usable in real organizations.

## 8. Conclusion

Multi-tenancy is not merely a hosting feature.

For DITA systems, it is an architectural commitment that must include:

- semantics
- graph behavior
- processing
- validation
- publishing
- and operational reproducibility

The toolchain registry exists to make those behaviors explicit.

ForgeDITA’s position is intentionally direct:

If a system cannot guarantee:

- reproducible publishing
- explicit semantics
- explainable processing
- and tenant isolation

then operational trust eventually erodes.

ForgeDITA is an attempt to build infrastructure that resists that erosion.

## 9. Request for Feedback

If you work with structured content systems at scale, your perspective would be valuable.

Questions:

- Where does this model become unrealistic?
- Which assumptions fail operationally?
- What hidden coupling risks remain?
- What governance burdens are underestimated?
- What practical failure modes should be explored further?

## Related

- Website: <https://forgedita.com>
- Contact: <hello@forgedita.com>

ForgeDITA is currently in architecture-first development toward MVP.

Structured content infrastructure designed for long-term operational maintainability.

---

Copyright 2026 ForgeDITA.
