# NATS JetStream Event Backbone

**Status: ARCHITECTURE AND OPERATING CONTRACT.**

Durable event messaging supports decoupled services, replay and explicit ACK/NAK processing. Since delivery can be at least once, consumers must be idempotent.

Recommended event metadata includes schema version, stable event ID, correlation ID, source, timestamp, provenance, retry budget and explicit execution authority. Event transport must not silently grant host administration rights.

Run authentication and authorization tests, disconnect/replay simulations and consumer restart tests before promoting mutating workflows.