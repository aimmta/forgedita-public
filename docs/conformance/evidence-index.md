# Conformance Evidence Model

ForgeDITA treats a conformance claim as the output of evidence, not as manually maintained marketing text.

## Evidence Record

Each claimed behavior should identify:

- stable claim identifier
- DITA specification track
- implementation version
- fixture or test case
- expected and actual result
- result status
- evidence timestamp
- hashes for relevant inputs and outputs
- known limitations

See the [illustrative evidence record](../../examples/conformance-evidence/example-evidence.json).

## Evidence Classes

1. **Specification fixtures:** representative DITA structures and semantics.
2. **Specialization fixtures:** behavior based on `@class` and declared domains.
3. **Round-trip fixtures:** import, storage, export, and re-import fidelity.
4. **Processor fixtures:** contextual resolution, filtering, and publishing behavior.
5. **Isolation fixtures:** tenant and bundle separation.
6. **Reproducibility fixtures:** identical named inputs produce the same fingerprint.
7. **Negative fixtures:** malformed, missing, ambiguous, and unauthorized conditions fail explicitly.
8. **Interoperability fixtures:** content from supported external sources behaves consistently.

## Promotion Policy

A passing prototype does not automatically become a public claim. Claim promotion requires repeatable evidence and an update to both the conformance matrix and public statement.

The files in `examples/` demonstrate intended evidence shapes. They are illustrative and must not be interpreted as current test results.
