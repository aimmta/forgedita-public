# Capability Discovery

Clients should not guess which operations, limits, and conformance boundaries apply to a deployment.

ForgeDITA exposes a capability-discovery contract at:

```http
GET /api/v1/capabilities
```

The response can identify:

- API version
- DITA specification track
- enabled feature groups
- supported import types and modes
- relevant tenant policies
- size or job limits
- feature status such as claimed, beta, planned, or unsupported

## Client Use

An editor can use capabilities to present only valid actions. An integration can fail early when a required operation is unavailable. Test tools can associate evidence with the exact feature boundary returned by the environment.

Capability discovery describes availability. It does not replace authorization, validation, or conformance evidence.
