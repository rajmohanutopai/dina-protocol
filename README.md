# Dina Protocol

[![Status: Draft](https://img.shields.io/badge/Status-Draft-orange)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **The open protocol for sovereign personal AI.**

Dina Protocol defines how personal AI agents identify themselves, protect user data, communicate with each other, and build trust -- without any platform in the middle.

Any implementation that follows this protocol can interoperate with any other: a Go Home Node can talk to a Rust one, a self-hosted Dina can coordinate with a managed one, and a specialist agent can plug in without owning the user's identity or policy layer.

---

## Why This Exists

Today's AI agents are powerful but lack a shared standard for:

- **Identity** -- who is this agent, and who does it serve?
- **Data sovereignty** -- who controls the user's data, and how is it protected?
- **Agent safety** -- how do we prevent agents from acting beyond their authority?
- **Inter-agent coordination** -- how do two personal AIs talk to each other securely?
- **Trust** -- how do we know if a recommendation, review, or agent is trustworthy?

Dina Protocol answers these questions with a layered interoperability contract, not just a wire format.

---

## Protocol Layers

| Layer | What It Defines |
|-------|----------------|
| **1. Identity** | BIP-39 mnemonic, SLIP-0010 key derivation under `m/9999'/...`, Ed25519 signing, DID representation (`did:plc`, `did:key`, `did:web`) |
| **2. Authentication** | Signed request envelopes with timestamp and nonce, replay protection, device pairing |
| **3. Encrypted Storage** | Per-persona SQLCipher vaults, HKDF-SHA256 derived DEKs, 4-tier access control (default/standard/sensitive/locked) |
| **4. Agent Safety** | Intent declaration before action, session-scoped grants, approval workflows, PII scrubbing, egress enforcement |
| **5. D2D Communication** | NaCl `crypto_box_seal` encrypted messaging between Home Nodes, dead-drop durability, request lifecycle states |
| **6. Trust Network** | Signed attestations on AT Protocol -- reviews, vouches, flags, verification, revocation |
| **7. Interaction Contract** | Portable semantic operations (`answer_now`, `delegate_task`, `communicate_person`, etc.) and typed response envelopes |

---

## Conformance Profiles

Not every implementation needs every layer. The protocol defines named profiles:

| Profile | Description |
|---------|-------------|
| **Home Node** | Full sovereign implementation -- all 7 layers |
| **Device** | Device identity, request signing, pairing; delegates storage and D2D to Home Node |
| **Agent Executor** | Authenticates to a Home Node, declares intent, operates within granted scope |
| **Provider Dina** | Service-facing identity with D2D endpoints for fulfilling requests |
| **Trust AppView** | Indexes and serves trust records; no vault or agent safety needed |
| **Bridge** | Maps non-native channels (email, chat) into Dina interaction semantics |
| **Mobile Home Node** | Full sovereignty on-device, delegates relay and AppView to hosted infrastructure |

An implementation is Dina-protocol-compliant with respect to its declared profiles, not by vague association.

---

## Repository Structure

```
protocol/
  spec/                  # Protocol specification (10 chapters)
    00-overview.html       # Scope, design goals, protocol shape
    01-identity.html       # Sovereign identity and key derivation
    02-authentication.html # Signed requests and replay protection
    03-storage.html        # Encrypted vault model
    04-agent-safety.html   # Intent, grants, approvals, egress
    05-d2d.html            # Dina-to-Dina encrypted messaging
    06-trust.html          # Trust Network on AT Protocol
    07-interaction.html    # Semantic operations and response envelopes
    08-profiles-and-scenarios.html  # Conformance profiles
    09-security-and-stability.html  # Security guarantees and freeze points
  schemas/               # Machine-readable JSON schemas
    capability-advertisement.json
    capability-negotiation.json
    auth-envelope.json
    d2d-envelope.json
    agent-intent.json
    approval-request.json
    response-envelope.json
  examples/              # Example JSON payloads
  dina-protocol.html     # Monolithic narrative reference
  agent-executor.html    # Agent executor profile spec
  agent-executor-checklist.html  # Conformance checklist for executors
  test-matrix.html       # Interoperability test matrix
```

---

## Reading Order

**Start here:** [The Complete Dina Protocol](https://rajmohanutopai.github.io/dina-protocol/protocol/dina-protocol.html) -- the full protocol in a single document covering all 7 layers, conformance profiles, and freeze points.

For the chapter-by-chapter breakdown:

1. [00-overview](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/00-overview.html) -- scope, design goals, and protocol shape.
2. [01-identity](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/01-identity.html) through [07-interaction](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/07-interaction.html) -- the full protocol specification.
3. [08-profiles-and-scenarios](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/08-profiles-and-scenarios.html) -- conformance targets.
4. [09-security-and-stability](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/09-security-and-stability.html) -- security guarantees and remaining freeze points.
5. [schemas](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/) and [examples](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/) -- build validators and interop tests.
6. [test matrix](https://rajmohanutopai.github.io/dina-protocol/protocol/test-matrix.html) -- verify conformance.

---

## Key Design Principles

**Minimal frozen core.** The protocol freezes only what must be identical across implementations: identity derivation, signed authentication, D2D envelope semantics, trust namespace, semantic operations, and response envelopes.

**Everything else is optional.** Profiles, extensions, and capabilities are named, discoverable, and negotiable. Unknown extensions never cause protocol failure.

**Capability negotiation before action.** Two implementations discover each other's capabilities and agree on a common subset before interacting -- no guessing.

**Sovereignty by default.** The user owns their identity, keys, and data. No platform mediates. Export is always available.

**Loyalty, not companionship.** Dina works for the user and nobody else. She is not an emotional companion -- when the user needs connection, she nudges toward humans.

---

## Reference Implementation

The reference implementation is [Dina](https://github.com/rajmohanutopai/dina) -- a Go Core + Python Brain + TypeScript AppView stack with 4,500+ tests. This protocol specification was extracted from that implementation and is intentionally broader: where the implementation still has open questions, the spec calls them out as **freeze points** for resolution before Protocol 1.0.

---

## Status

This protocol is in **draft**. The specification is reference-implementation-driven and is being refined toward a 1.0 freeze.

Open freeze points include:
- Root key material profile (raw entropy vs. PBKDF2-expanded seed)
- D2D request-state plane standardization
- Trust core namespace subset finalization
- Capability discovery and negotiation schema freeze
- Interaction operation registry standardization
- Response envelope schema finalization

---

## Contributing

This is an open protocol. Contributions, feedback, and alternative implementations are welcome.

---

## License

[MIT](https://rajmohanutopai.github.io/dina-protocol/LICENSE)

---

*Dina was first imagined in a [novel](https://github.com/rajmohanutopai/utopai/blob/main/UTOPAI_2017_full.pdf) written between 2012-2017. This protocol is being built in the open to make her real.*
