# Product Principles

ForgeDITA is governed by a small set of architectural commitments.

## Native Content

Standards-compliant DITA XML remains the system of record. Platform features must not require proprietary wrapper markup or a private canonical document model.

## Explicit Behavior

Semantics, validation rules, processing assets, and publishing profiles should be declared, versioned, and inspectable. Behavior that affects an output should not depend on undocumented runtime state.

## Reproducible Publishing

A publication should identify the content baseline, toolchain bundle, publish profile, and relevant semantic state used to produce it.

## Graph Awareness

DITA is a network of contextual relationships. Keys, conrefs, maps, subject schemes, filters, and reuse dependencies must be modeled as such.

## Open Clients

Oxygen is a first-class client, not the source of platform truth. Every material authoring operation should be available through documented server APIs.

## Bounded Claims

ForgeDITA claims only the behavior listed in its public conformance statement. Partial and unsupported behavior must remain visible as partial and unsupported.

## Clean Exit

Content, relevant metadata, and tenant-owned toolchain assets should be exportable without a proprietary decoding step or mandatory professional-services engagement.

## Operational Maintainability

Architecture decisions should be evaluated by their long-term effect on clarity, upgradeability, diagnosis, and recovery, not only by short-term convenience.
