# Event Bus and Service Integration

**Status: ARCHITECTURE AND CONSTRAINTS; individual deployments need separate evidence.**

An event and integration bus can decouple producers, durable messaging, adapters, consumers and audit trails. NATS JetStream is an example of a lightweight event backbone.

## Design rules
- At-least-once delivery means consumers must be idempotent.
- Events and commands have different authority. Receiving an event does not grant permission to change a host.
- Preserve schema version, correlation ID, source provenance, freshness, retries and terminal failure reasons.
- Keep budgets and credential authority at integration boundaries, not in shared message payloads.
- Replay and dead-letter behavior must be tested under partial failure and disconnection.

## Rollout
Specify contracts, simulate producers and consumers, stage with read-only data, validate access policy and replay behavior, then gate any mutating command path separately.