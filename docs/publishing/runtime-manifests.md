# Runtime Manifests

A runtime manifest records what a publish job used and produced.

## Minimum Record

- job and tenant identifiers
- baseline identity and revision-set hash
- toolchain bundle identity and manifest hash
- publish profile
- semantic registry version
- job timestamps and outcome
- artifact inventory with hashes
- publish fingerprint
- diagnostic summary

The manifest is a first-class release artifact. It should not require log archaeology or human memory to reconstruct the build.

See the [illustrative runtime manifest](../../examples/runtime-manifest/runtime-manifest.json). The example values are fictional and are not evidence of a completed product release.
