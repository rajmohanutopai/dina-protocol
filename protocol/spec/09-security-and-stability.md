# 09 Security And Stability

This chapter defines protocol-level guarantees, remaining freeze points, and current reference implementation status.

## Security Properties

At protocol level, Dina aims to guarantee:

- sovereign identity under user-controlled root material
- request authenticity via Ed25519
- replay protection
- encryption at rest per persona
- explicit safety mediation for risky agent access
- strong D2D confidentiality and authenticity
- attributable public trust publication
- portable interaction semantics above transport

## Open Standardization Questions

### Core Freeze Points

These should be resolved before calling the core protocol frozen:

1. Root key material profile
   - raw mnemonic entropy vs PBKDF2-expanded BIP-39 seed
   - recommended 1.0 choice: 24-word BIP-39 mnemonic as recovery secret,
     PBKDF2-expanded BIP-39 seed as canonical root key material,
     with SLIP-0010 downstream derivation from that seed

2. D2D generic request-state plane
   - freeze request modes, lifecycle states, and required fields for federated execution
   - recommended 1.0 choice: request modes `inline_preferred`, `async`, `subscription`;
     lifecycle states `accepted`, `waiting_approval`, `waiting_input`, `running`,
     `partial`, `completed`, `failed`, `cancelled`, `expired`;
     required fields `request_id`, `correlation_id`, `requested_capability`,
     `response_mode`, `status`, `created_at`, `updated_at`

3. Trust core collection subset
   - define the 1.0 mandatory namespace subset
   - recommended 1.0 choice: `com.dina.trust.attestation`,
     `com.dina.trust.vouch`, `com.dina.trust.flag`,
     `com.dina.trust.revocation`, `com.dina.trust.verification`

4. Capability discovery and negotiation
   - freeze the capability advertisement contract, profile identifiers,
     extension namespace rules, and live negotiation semantics
   - recommended 1.0 choice: static capability advertisement plus live negotiation,
     with profile identifiers at least for `home-node`, `device`, `agent-executor`,
     `provider-dina`, `trust-appview`, `bridge`, and `mobile-home-node`

5. Interaction operation registry
   - freeze the required core semantic operation set and extension rules
   - recommended 1.0 choice: freeze semantic names and contracts for
     `remember_context`, `answer_now`, `propose_plan`, `communicate_person`,
     `request_service`, `delegate_task`, `watch_condition`, `approve_operation`,
     `inspect_status`, `modify_existing`, `cancel_operation`, `recover_operation`,
     `deliver_briefing`, `deliver_alert`, `deliver_update`, `deliver_completion`,
     `deliver_failure`, `request_clarification`, `escalate_attention`

6. Response envelope schema
   - freeze the portable typed response envelope and its extensibility model
   - recommended 1.0 choice: response kinds `summary`, `list`, `table`,
     `comparison`, `status`, `error`, `raw`; required envelope fields
     `schema_version`, `type`, `text`, `data`; optional standardized fields
     `title`, `meta`, `annotations`, `disclosures`, `extensions`

### Profile and Portability Questions

These affect optional profiles, not base protocol interoperability:

7. Encrypted vault portability profile
   - decide whether Protocol 1.0 will standardize canonical DEK derivation
     for encrypted vault interchange and seed-based encrypted recovery
   - otherwise keep logical export/import as the required storage portability contract

8. Mobile storage profile
   - define minimum SQLCipher-equivalent encryption
     for iOS Keychain / Android Keystore integration
   - define background/foreground lifecycle rules
     for DEK management on mobile

## What "Dina as Protocol" Means

When this protocol is implemented correctly:

- a Rust Home Node can interoperate with a Go Home Node
- a Swift device can sign requests against any Dina-compatible node
- a browser bridge can participate in interaction semantics without owning vault authority
- one implementation can discover another implementation's supported profiles and extensions before attempting optional interop
- one Dina can ask another Dina for an outcome, and the remote Dina can satisfy it through its own tools or executors
- any AppView that indexes `com.dina.trust.*` can serve trust for any Dina-compatible identity
- vaults, trust records, D2D messages, and interaction semantics stop being implementation-private details

That is the transition from product to infrastructure.

## Reference Implementation Status

The current Dina codebase already provides substantial pieces of this protocol:

- SLIP-0010 `m/9999'` identity derivation
- `did:plc` and `did:key` usage
- Ed25519 request signing
- pairing and device registration
- per-persona encrypted storage
- HNSW hydration on unlock and destruction on lock
- session-scoped grants and approvals
- PII scrubbing
- D2D signatures, encryption, outbox, and policy gates
- AT Protocol trust publishing and AppView query integration
- a much more precise interaction architecture than a simple "assistant command" model

The protocol document is therefore not speculative. It is extracted from a real system, while also identifying the exact points where the system still needs one canonical choice before the protocol can be declared stable.
