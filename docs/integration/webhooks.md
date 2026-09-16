# Events and Webhooks

ForgeDITA integrations should react to durable domain events rather than scrape UI state or poll undocumented data.

## Candidate Events

- `document.changed`
- `baseline.created`
- `release.promoted`
- `publish.completed`
- `publish.failed`
- `workflow.transitioned`
- `toolchain.promoted`

## Delivery Contract

Production webhook delivery should include:

- stable event identifier
- event type and schema version
- tenant and applicable workspace scope
- resource identifier
- occurrence timestamp
- correlation identifier
- signed delivery
- retry and idempotency guidance

## Status

This page describes the target integration contract. Individual event availability must be discovered through the product capability surface.
