# Product Status

Last updated: September 2026

ForgeDITA is in architecture-first development toward an MVP. This page separates current claims from architectural intent.

## Current Claims

The current public claim boundary is maintained in the [Public Conformance Statement](docs/conformance/public-conformance-statement.md). It includes bounded support for native repository objects, core authoring operations, prototype validation coordination, graph extraction, preview and publish jobs, lightweight workflow, workspace search, and capability discovery.

## Prototype-Level Areas

These areas have executable paths but are not yet production guarantees:

- layered validation across toolchain assets
- semantic-registry-backed graph extraction
- basic map context and key lookup
- impact analysis
- DITA-OT-backed preview and publish jobs
- search facets for selected DITA metadata

## Planned Production Work

- hardened asynchronous publish workers
- stronger runtime isolation and cancellation
- production object storage
- expanded conformance fixtures
- complete contextual reference-resolution coverage
- broader specialization and subject-scheme coverage
- packaged Oxygen integration
- production-grade search and ranking

## Explicitly Not Claimed

- universal DITA 1.3 conformance
- DITA 2.0 conformance
- replacement of dedicated regulated approval systems
- complete branch-aware repository semantics
- complete conref, conkeyref, keyref, and key-scope behavior
- production certification or independent security attestation

## Reading Architecture Documents

Architecture documents describe the intended operating model. They are design contracts and review material, not proof that every described behavior is currently implemented.
