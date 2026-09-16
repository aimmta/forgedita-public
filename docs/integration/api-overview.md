# API Overview

ForgeDITA is API-first. The Oxygen integration and administrative interfaces consume the same server contracts available to other authorized clients.

## API Families

| API | Responsibility |
| --- | --- |
| Repository | Browse, create, move, version, lock, checkout, and check-in |
| Content | Read and save XML or binary content; retrieve map context |
| Graph | Inspect references, resolve keys, and request impact analysis |
| Validation | Validate content states and return structured diagnostics |
| Preview | Render working content in a named context and profile |
| Publish | Submit and inspect attributable publishing jobs |
| Baseline and Release | Create, compare, promote, and rebuild immutable states |
| Workflow | Assign, review, approve, comment, and transition |
| Package Registry | Upload, validate, promote, and inspect toolchain bundles |
| Search | Query content, metadata, state, and supported DITA structures |
| Events | Notify authorized integrations of durable state changes |
| Authentication | Map OIDC identities and scoped service principals to permissions |

## Contract Rules

- tenant and workspace scope is explicit
- authorization is enforced server-side
- errors use stable codes and correlation identifiers
- partial, unavailable, and unsupported results are distinct
- capability discovery precedes optional behavior
- versioning protects clients from silent contract changes

Endpoint details remain implementation-version-specific. The [Editor Authoring Contract](../editor-authoring-contract.md) defines the client-facing behavioral contract.
