# Reproducible Publishing

ForgeDITA models publishing as a function of named, versioned inputs.

```text
content baseline
+ toolchain bundle
+ publish profile
+ declared runtime inputs
= attributable publish job
```

## Required Inputs

- immutable content baseline or explicit working state for preview
- immutable toolchain bundle version
- named publish profile
- semantic registry version
- declared filtering and parameter inputs

## Required Outputs

- published artifacts
- logs and structured diagnostics
- artifact inventory and hashes
- runtime manifest
- publish fingerprint

## Rebuild

A release rebuild should select the original baseline, bundle, and profile. The resulting artifacts can then be compared with the original inventory. A difference is acceptable only when the changed input or permitted runtime variance is identified.

## Boundary

Deterministic fingerprints, queued workers, signed artifacts, retry and cancellation, runtime manifests, and container-oriented runtime confinement are implemented at the current beta gate. Environment-specific production acceptance and complete reproducibility coverage remain subject to the [current status](../../STATUS.md) and conformance evidence.
