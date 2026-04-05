# Dina Protocol

Status: draft, reference-implementation-driven

This directory contains the Dina protocol in split chapter form plus machine-readable schemas.

## Structure

### Spec
- [00-overview.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/00-overview.md)
- [01-identity.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/01-identity.md)
- [02-authentication.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/02-authentication.md)
- [03-storage.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/03-storage.md)
- [04-agent-safety.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/04-agent-safety.md)
- [05-d2d.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/05-d2d.md)
- [06-trust.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/06-trust.md)
- [07-interaction.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/07-interaction.md)
- [08-profiles-and-scenarios.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/08-profiles-and-scenarios.md)
- [09-security-and-stability.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/09-security-and-stability.md)

### Schemas
- [capability-advertisement.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/capability-advertisement.json)
- [capability-negotiation.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/capability-negotiation.json)
- [auth-envelope.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/auth-envelope.json)
- [d2d-envelope.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/d2d-envelope.json)
- [agent-intent.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/agent-intent.json)
- [approval-request.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/approval-request.json)
- [response-envelope.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/response-envelope.json)

### Examples
- [README.md](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/README.md)
- [capability-advertisement.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/capability-advertisement.example.json)
- [agent-executor-capability.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/agent-executor-capability.example.json)
- [agent-intent.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/agent-intent.example.json)
- [capability-negotiation.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/capability-negotiation.example.json)
- [auth-envelope.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/auth-envelope.example.json)
- [d2d-envelope.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/d2d-envelope.example.json)
- [approval-request.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/approval-request.example.json)
- [response-envelope.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/response-envelope.example.json)

### Conformance
- [test-matrix.md](https://rajmohanutopai.github.io/dina-protocol/protocol/test-matrix.md)
- [agent-executor.md](https://rajmohanutopai.github.io/dina-protocol/protocol/agent-executor.md)
- [agent-executor-checklist.md](https://rajmohanutopai.github.io/dina-protocol/protocol/agent-executor-checklist.md)

## Reading Order

1. Read [00-overview.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/00-overview.md).
2. Read [01-identity.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/01-identity.md) through [07-interaction.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/07-interaction.md).
3. Use [08-profiles-and-scenarios.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/08-profiles-and-scenarios.md) to understand conformance targets.
4. Use [09-security-and-stability.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/09-security-and-stability.md) to understand guarantees and remaining freeze points.
5. Use [examples/README.md](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/README.md) and [test-matrix.md](https://rajmohanutopai.github.io/dina-protocol/protocol/test-matrix.md) to build validators and interop tests.

## Compatibility Model

The protocol is intentionally shaped as:
- minimal frozen core
- named profiles
- namespaced extensions
- capability discovery and negotiation

That keeps the interoperable base stable without forcing every Dina-compatible implementation into one product shape.

The monolithic narrative reference remains at [dina-protocol.md](https://rajmohanutopai.github.io/dina-protocol/protocol/dina-protocol.md).
