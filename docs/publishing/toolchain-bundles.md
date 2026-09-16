# Toolchain Bundles

A toolchain bundle is a versioned description of a tenant publishing and validation environment.

## Typical Contents

- DITA-OT version
- plugins and their versions
- XML catalogs
- DITA grammar shells and constraints
- Schematron and policy rules
- DITAVAL presets
- presentation themes
- publish profiles
- semantic registry reference

## Lifecycle

1. Upload a candidate bundle.
2. Validate its manifest and declared dependencies.
3. Exercise representative fixtures.
4. Promote an accepted version.
5. Treat the promoted version as immutable.
6. Retain versions required by historical releases.

## Isolation Rule

Tenant A must not change Tenant B's processing behavior. Sharing is permitted only through explicit, versioned inheritance, never through an ambient mutable server installation.

See the [example bundle manifest](../../examples/toolchain-bundle-manifest/toolchain-bundle.yaml).
