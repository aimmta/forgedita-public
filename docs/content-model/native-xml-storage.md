# Native XML Storage

ForgeDITA treats DITA XML as the authoritative content representation.

## Rules

- content is stored and retrieved as standards-compliant XML
- platform features do not require proprietary elements or namespaces
- resource identity is maintained outside the XML when possible
- binary assets remain binary repository objects
- exports include native files and a readable manifest
- import and export do not require a proprietary decoding service

## Fidelity

Round-trip evidence should distinguish byte identity from semantic identity. Harmless serialization differences such as attribute order may prevent byte identity while preserving XML meaning. ForgeDITA should report which standard is being asserted rather than treating them as equivalent.

## Portability

Native storage alone does not guarantee an inexpensive migration. Catalogs, grammars, plugins, validation rules, profiles, and semantic declarations also influence behavior. ForgeDITA therefore treats tenant-owned toolchain material as part of the exit path.
