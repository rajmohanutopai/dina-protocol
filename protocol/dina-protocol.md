# Dina Protocol

Status: draft, reference-implementation-driven

This document defines Dina as a protocol rather than a single product implementation.

The goal is:

- any implementation can create a Dina-compatible identity
- any implementation can authenticate to a Dina-compatible Home Node
- any implementation can store, import, and export sovereign vault data safely
- any implementation can enforce the same agent-safety contract
- any implementation can participate in Dina-to-Dina coordination
- any implementation can publish to and query the Trust Network
- any implementation can interoperate at the interaction layer, not just the transport layer

This document is intentionally broader than the current Go/Python/TypeScript reference implementation. Where the implementation already behaves consistently, the document is normative. Where the implementation still contains multiple active profiles or migration-era behavior, the document calls that out explicitly so the protocol can be frozen cleanly later.

## 1. Scope

Dina the product is a sovereign personal AI.

Dina the protocol is the standard for how humans and their software:

- identify themselves
- authenticate
- protect personal data
- authorize agent access
- coordinate with other sovereign identities
- publish and query trust
- express interactions in a portable way

This protocol is not only a wire format. It is a layered interoperability contract.

## 2. Design Goals

The protocol exists so that:

- a Go, Rust, Swift, Python, or browser-based implementation can interoperate
- a managed hosted Dina and a self-hosted Dina can still speak the same contracts
- a specialist agent can plug into Dina without owning Dina's identity or policy layer
- a D2D request can be fulfilled by a remote Dina regardless of its internal executor
- the Trust Network remains portable across AppViews and Home Nodes
- user interaction semantics remain stable even when implementations differ internally
- implementations can advertise what they support before attempting advanced interop
- optional features can evolve without fragmenting the core protocol

## 3. Relationship to the Interaction Architecture

This protocol is grounded in the interaction architecture already defined for Dina:

- the full interaction inventory
- the consolidated domain model
- the D2D domain model
- the interaction topology model
- context resolution
- response formatting

Those documents answer:

- what users and Dinas need to do
- who they interact with
- how requests are grounded
- how results are rendered

This protocol document answers a different question:

- what independent implementations must agree on to interoperate across all of that

## 4. Conformance Language

The keywords MUST, SHOULD, MAY, MUST NOT, and SHOULD NOT are used in the RFC sense.

This document distinguishes:

- Protocol contract: what any conforming implementation must do
- Reference behavior: what the current Dina implementation does today
- Open freeze point: a place where the reference implementation still needs one protocol choice to be standardized before a 1.0 release
- Core protocol: the minimum frozen subset every conforming implementation shares
- Profile: a named, optional capability bundle built on top of the core protocol
- Extension: a namespaced optional feature that may be ignored when unsupported
- Profile-qualified compliance: an implementation is Dina-protocol-compliant only with respect to one or more declared profiles, not by vague association with Dina

## 5. Protocol Shape

The Dina protocol should be strict only where interoperability actually requires strictness.

Its shape is:

- minimal frozen core
- named conformance profiles
- namespaced extensions
- capability discovery and negotiation
- layered interoperability contracts

### 5.1 Minimal Frozen Core

The core protocol should freeze only the pieces that must be identical across implementations:

- identity derivation
- signed request authentication
- minimum D2D envelope and lifecycle semantics
- minimum trust namespace subset
- minimum semantic operation set
- minimum response envelope core

Everything else should remain profile-based or extensible.

### 5.2 Profiles

Profiles are named bundles of additional behavior on top of the core protocol.

Examples include:

- Home Node
- Device
- Agent Executor
- Provider Dina
- AppView
- Bridge
- Mobile Home Node
- encrypted vault portability

Profiles allow an implementation to be fully Dina-compatible without pretending to support every optional behavior.

### 5.3 Extensions and Namespacing

The protocol SHOULD allow growth through explicit extension points.

Extensions SHOULD be:

- namespaced
- discoverable
- ignorable when unsupported
- non-breaking to the core protocol

Unknown optional extensions MUST NOT cause protocol failure by themselves.

Failure should occur only when:

- the remote side requires a capability the local side does not support
- or no acceptable common profile can be negotiated

### 5.4 Capability Discovery and Negotiation

Conforming implementations SHOULD support both:

- static capability advertisement
- live capability negotiation

Static capability advertisement answers:

- what this implementation can do in general
- which profiles it implements
- which optional features it understands

Live capability negotiation answers:

- what these two parties will use for this interaction

At minimum, a capability advertisement SHOULD declare:

- supported protocol versions
- implemented conformance profiles
- supported authentication methods
- supported D2D message families
- supported semantic operations
- supported request modes and lifecycle states
- supported response envelope versions and response kinds
- supported response extensions and disclosure annotations
- supported trust namespace subsets
- delivery capabilities such as mailbox relay or proactive delivery
- optional storage portability profiles
- relevant limits such as payload size, attachment size, or timeout windows

Capability advertisement MAY be published through:

- DID service metadata
- a dedicated capability document
- a service discovery endpoint
- another equivalent discovery mechanism

The protocol should standardize the semantics of capability discovery, not require one single UI or deployment shape.

Draft machine-readable artifacts for this layer live in:

- `schemas/capability-advertisement.json`
- `schemas/capability-negotiation.json`

Live negotiation SHOULD choose, for a given interaction:

- the protocol version
- the authentication method
- the message family or request family
- the request mode
- the response envelope version
- any required extensions
- fallback behavior if advanced features are unavailable

Two Dina-compatible implementations MUST be able to fall back to their common subset whenever possible.

An implementation MUST be able to say:

- what it supports
- what it requires
- what it selected for this interaction

without forcing the remote side to guess.

### 5.5 Protocol Layers

Dina has six core protocol layers and one cross-cutting interoperability layer.

1. Identity
2. Authentication
3. Encrypted Storage
4. Agent Safety
5. Dina-to-Dina Communication
6. Trust Network
7. Interaction Contract

The first six are the infrastructure core.

The seventh is the application interoperability layer that makes all higher-level scenarios portable across implementations.

## 6. Core Entities

The protocol recognizes the following principal types:

- Human root identity
- Home Node
- Device
- Internal service
- Agent executor
- Remote Dina
- Provider or organization Dina
- Trust AppView

The protocol also recognizes the following counterparty classes:

- Person
- Provider
- Institution
- Public infrastructure
- Device or IoT system
- Agent or executor

These matter because the same user request may route through different fulfillment topologies while preserving the same external contract.

Every conforming implementation SHOULD also expose enough capability metadata for another Dina-compatible implementation to determine:

- which principal and counterparty roles it can play
- which profiles it implements
- which optional features are safe to attempt

## 7. Layer 1: Identity

### 7.1 Purpose

Identity defines who a sovereign participant is and how other implementations recognize that participant.

### 7.2 Protocol Requirements

A Dina-compatible identity system MUST define:

- a recoverable root secret
- deterministic key derivation rules
- a DID representation
- a DID document or equivalent resolution record
- a key rotation model
- service endpoint discovery

### 7.3 Root Secret Model

The intended Dina identity model is:

- generate a BIP-39 mnemonic
- derive root key material from that mnemonic
- derive protocol-specific subkeys under a Dina-specific purpose path

The Dina-specific HD namespace is:

- `m/9999'/0'/...` root identity signing
- `m/9999'/1'/...` persona and sub-identity signing
- `m/9999'/2'/...` PLC recovery / rotation
- `m/9999'/3'/...` service-to-service authentication

This prevents collision with wallet ecosystems such as BIP-44.

### 7.4 Current Reference Implementation

The current Dina reference implementation already uses:

- BIP-39 recovery phrases on the CLI side
- SLIP-0010 hardened derivation under `m/9999'/...`
- Ed25519 for identity and service signing
- secp256k1 for PLC recovery / rotation keys

Concrete derivation paths in the current implementation include:

- `m/9999'/0'/0'` root signing generation 0
- `m/9999'/0'/<generation>'` subsequent root signing generations
- `m/9999'/1'/<personaIndex>'/<generation>'` persona signing
- `m/9999'/2'/<generation>'` PLC recovery / rotation
- `m/9999'/3'/<serviceIndex>'` service authentication

### 7.5 DID Methods

A Dina-compatible Home Node SHOULD expose a `did:plc` identity.

Device and service principals MAY use:

- `did:key`
- `did:plc`
- `did:web`

The current reference implementation accepts all three DID prefixes at the domain layer, while using `did:plc` as the primary Home Node identity and `did:key` for CLI device identities.

### 7.6 DID Document Requirements

A Dina-compatible DID document SHOULD expose:

- one or more Ed25519 verification methods in multikey format
- authentication references
- service endpoints for Dina messaging and other declared capabilities

The reference implementation currently uses:

- Ed25519 multikeys with the `0xed01` multicodec prefix
- a Dina messaging service endpoint, typically `DinaMsgBox`

### 7.7 Rotation and Recovery

Home Node identities MUST support key rotation.

For `did:plc`, rotation SHOULD be driven through a recovery key distinct from the root signing key.

The current reference implementation derives PLC recovery keys from the dedicated `m/9999'/2'/...` subtree and supports PDS/PLC-backed DID lifecycle operations.

### 7.8 Protocol Freeze Point

The reference implementation currently has an unresolved identity-root question:

- some code paths treat the root as raw 32-byte entropy rendered as a BIP-39 mnemonic
- some documentation and tests treat the root as the 64-byte BIP-39 PBKDF2-expanded seed

Before Dina Protocol 1.0 is frozen, the protocol MUST standardize one root key material profile for downstream derivation.

Recommended direction:

- recovery secret: 24-word BIP-39 mnemonic
- canonical root key material: BIP-39 PBKDF2-expanded seed
- deterministic subkey derivation from that canonical root key material

## 8. Layer 2: Authentication

### 8.1 Purpose

Authentication defines how a participant proves who it is.

### 8.2 Protocol Requirements

A Dina-compatible authentication profile MUST support signed requests using Ed25519.

The canonical signing payload is:

```text
METHOD
PATH
QUERY
TIMESTAMP
NONCE
SHA256_HEX(BODY)
```

The current reference implementation signs exactly this payload.

A draft normalized schema for signed-request interoperability lives in:

- `schemas/auth-envelope.json`

### 8.3 HTTP Header Contract

A signed HTTP request SHOULD carry:

- `X-DID`
- `X-Timestamp`
- `X-Nonce`
- `X-Signature`

`X-Signature` is the Ed25519 signature over the canonical payload.

### 8.4 Replay Protection

A conforming implementation MUST protect against replay.

The reference implementation currently enforces:

- a 5-minute clock skew window
- a per-request nonce
- a double-buffer nonce cache for O(1) rotation and replay rejection

### 8.5 Device Pairing

A Dina-compatible Home Node SHOULD support device pairing as a bootstrap flow.

The reference implementation currently uses:

- a 6-digit pairing code
- 5-minute TTL
- single-use semantics
- a cryptographic secret behind the visible code

Pairing MAY result in either:

- a registered Ed25519 device key
- a paired bearer token for compatibility or bootstrap flows

### 8.6 Service Keys

Internal or peer services MAY authenticate with distinct service Ed25519 keypairs.

This is already present in the reference implementation for Core-to-Brain and similar service paths.

### 8.7 Auth Modes

The Dina protocol recognizes at least these auth modes:

- device_signature
- service_signature
- paired_token
- browser_session

The protocol SHOULD treat signature-based authentication as the primary interoperable profile.

Paired bearer tokens are acceptable as a bootstrap or compatibility profile, but SHOULD NOT be the normative long-term cross-implementation trust model.

### 8.8 Caller Model

A Dina-compatible implementation SHOULD propagate caller context containing at least:

- principal
- actor
- origin
- auth_mode

This becomes important for:

- vault access policy
- approvals
- audit
- D2D relaying
- executor mediation

## 9. Layer 3: Encrypted Storage

### 9.1 Purpose

Encrypted Storage defines how personal data is protected and made portable across implementations.

### 9.2 Protocol Requirements

A Dina-compatible storage system MUST provide:

- per-persona isolation
- encryption at rest
- typed vault records
- provenance and trust fields
- staging / pending approval support
- import/export at the logical schema level

### 9.3 Persona Model

The protocol persona tiers are:

- `default`
- `standard`
- `sensitive`
- `locked`

These tiers are already present in the reference implementation.

Expected semantics:

- `default`: always open, minimal friction
- `standard`: open by policy, agent access requires scoped grants
- `sensitive`: closed by default, requires approval
- `locked`: passphrase or explicit unlock, agent access denied or highly restricted

### 9.4 Physical Storage Profile

A Dina-compatible Home Node SHOULD use an encrypted SQL store per persona.

The reference implementation uses SQLCipher-backed SQLite databases, one per persona, with encrypted-at-rest FTS and embedding storage.

### 9.5 Logical Vault Schema

A Dina-compatible vault item model MUST be able to express:

- stable ID
- item type
- content
- metadata
- tags
- timestamps
- source and provenance
- trust and confidence
- retrieval policy
- optional embeddings
- optional redacted or tiered content views

The reference implementation's persona schema currently includes logical fields such as:

- `id`
- `type`
- `source`
- `source_id`
- `contact_did`
- `summary`
- `body`
- `metadata`
- `tags`
- `timestamp`
- `created_at`
- `updated_at`
- `sender`
- `sender_trust`
- `source_type`
- `confidence`
- `retrieval_policy`
- `content_l0`
- `content_l1`

The protocol SHOULD freeze the logical schema, not the exact SQL DDL.

### 9.6 Search and Semantic Hydration

The protocol SHOULD support:

- lexical search
- semantic retrieval
- staging before persistence

The reference implementation currently provides:

- FTS5 over summary, body, tags, and contact surfaces
- HNSW vector search loaded only while a persona is unlocked

Important storage rule:

- embeddings MUST be encrypted at rest
- in-memory vector indices MUST be destroyed on persona lock

### 9.7 Backup and Export

Base Dina storage interoperability is logical, not file-format-level.

At minimum, conforming implementations MUST support:

- logical export of vault contents, metadata, provenance, and trust fields
- logical import of that same material into another Dina-compatible implementation
- explicit user intent for any decrypted export
- clear marking of insecure or plaintext exports

Implementations MUST NOT create silent plaintext backups as a side effect of normal backup or migration.

Safe profiles therefore separate:

- logical portability: required for protocol interoperability
- encrypted vault portability: optional profile, if implementations want direct encrypted file interchange or seed-based vault recovery

### 9.8 Encrypted Vault Portability Profile

The current implementation has more than one active DEK derivation style:

- an HKDF profile of the form `dina:vault:<persona>:v1` with a user salt
- a versioned persona profile of the form `dina:persona:<name>:dek:vN` with deterministic persona-scoped salt

This matters only if the protocol wants to guarantee one of the following:

- opening an encrypted vault file across implementations
- seed-based recovery of encrypted vault material without logical export
- encrypted backup portability without re-encryption

Those guarantees are useful, but they are not required for base Dina interoperability.

Therefore Protocol 1.0 MAY defer canonical DEK derivation at the core layer, provided it freezes:

- the logical vault schema
- the logical export/import contract
- explicit metadata describing any encrypted-vault portability profile

If an encrypted vault portability profile is later standardized, it SHOULD define:

- the canonical DEK derivation profile
- DEK version metadata
- migration and re-encryption rules
- profile negotiation or profile declaration during import/export

## 10. Layer 4: Agent Safety

### 10.1 Purpose

Agent Safety defines how agents interact with personal data and external systems without collapsing user sovereignty.

### 10.2 Protocol Requirements

A Dina-compatible safety layer MUST provide:

- session-scoped access
- approval requests
- scoped grants
- intent evaluation
- egress controls
- PII scrubbing
- auditability

### 10.3 Session and Grant Model

The reference implementation already models:

- named agent sessions
- active vs ended session state
- grants scoped to a session
- revocation of all grants on session end

An interoperable session model SHOULD include:

- `id`
- `name`
- `agent_did`
- `status`
- `created_at`
- `last_activity_at`
- `ended_at`

An interoperable access grant SHOULD include:

- `id`
- `client_did`
- `persona_id`
- `session_id`
- `scope`
- `expires_at`
- `granted_by`
- `reason`

Grant scopes SHOULD at least distinguish:

- single
- session

### 10.4 Approval Model

An approval request MUST be portable across implementations.

Minimum approval fields SHOULD include:

- `id`
- `type`
- `client_did`
- `persona_id`
- `session_id`
- `action`
- `scope`
- `status`
- `reason`
- `preview`
- `granted_by`
- `expires_at`
- `created_at`
- `updated_at`

The reference implementation already exposes this shape.

A draft machine-readable schema for this contract lives in:

- `schemas/approval-request.json`

Approval types currently include:

- `persona_access`
- `action_intent`

### 10.5 Intent Evaluation

A Dina-compatible agent safety layer MUST require agents to declare intent.

The current reference intent model is:

- `agent_did`
- `action`
- `target`
- `persona_id`
- `trust_level`
- `constraints`

The current reference decision model is:

- `allowed`
- `reason`
- `audit`

A draft machine-readable schema for executor intent declaration lives in:

- `schemas/agent-intent.json`

### 10.6 Safety Rules

At protocol level, Dina safety has four core principles:

- safe reads and reasoning may pass silently
- risky actions require review
- untrusted agents are denied by default
- raw sensitive data must not leave the node without policy checks

The reference gatekeeper already treats actions such as:

- `send_email`
- `transfer_money`
- `share_data`

as risky actions that require explicit user review.

### 10.7 PII Scrubbing

A Dina-compatible implementation MUST support PII scrubbing before cloud or third-party egress.

The reference implementation currently provides:

- Tier 1: regex-based scrubbing for structured PII
- Tier 2: Brain-assisted structured or NER-assisted scrubbing

The protocol SHOULD allow additional tiers, but MUST preserve the contract that scrubbed text can be safely passed onward and later rehydrated only within an authorized local context.

### 10.8 Audit

Every approval, denial, sensitive access, or denied egress SHOULD be auditable.

This is already present in the reference implementation and should remain a required interoperability expectation for serious implementations.

## 11. Layer 5: Dina-to-Dina Communication

### 11.1 Purpose

D2D defines how two sovereign identities coordinate securely.

It is not just a transport channel. It is a policy-aware, trust-aware coordination layer between two human-centered systems.

### 11.2 Protocol Requirements

A Dina-compatible D2D layer MUST provide:

- DID resolution for endpoint discovery
- sender authentication
- message confidentiality
- typed message families
- replay protection
- outbox / offline delivery semantics
- contact and sharing policy enforcement

### 11.3 Wire Model

The reference implementation currently uses:

- Ed25519 signatures over plaintext messages
- Ed25519-to-X25519 conversion for encryption
- NaCl sealed box encryption
- a JSON wrapper containing ciphertext and signature

The current transport wrapper is effectively:

```json
{
  "c": "<base64 ciphertext>",
  "s": "<hex ed25519 signature over plaintext>"
}
```

The plaintext message model is currently:

```json
{
  "id": "msg-123",
  "type": "coordination.request",
  "from": "did:plc:sender",
  "to": ["did:plc:recipient"],
  "created_time": 1710000000,
  "body": { ... }
}
```

### 11.4 Message Families

The current Dina D2D v1 families are:

- `presence.signal`
- `coordination.request`
- `coordination.response`
- `social.update`
- `safety.alert`
- `trust.vouch.request`
- `trust.vouch.response`

These are the transport-level v1 families the current implementation accepts as its strict D2D surface.

### 11.5 Message Policy

The current scenario policy model is:

- `standing_policy`
- `explicit_once`
- `deny_by_default`

Default scenario families in the reference implementation currently map roughly to:

- presence: standing
- coordination: standing
- social: standing
- safety: standing
- trust: explicit_once

`safety.alert` is treated as a special always-pass inbound case.

### 11.6 Contact and Sharing Policy

D2D interoperability requires more than cryptography.

It also requires:

- explicit contact state
- trust ring
- entity resolution mode
- per-category sharing policy

The reference implementation already models:

- trust rings
- resolution modes such as late binding vs plaintext
- per-category sharing tiers

This is critical for person-to-person interoperability because "can I reach this Dina" and "what may I disclose to this Dina" are different questions.

### 11.7 Outbox and Offline Delivery

A conforming implementation SHOULD support durable offline delivery.

The reference implementation already has:

- durable outbox records
- retries with exponential backoff
- pending approval state
- delivered vs failed state
- support for a Dina message box endpoint

Outbox states currently include:

- `pending`
- `pending_approval`
- `sending`
- `delivered`
- `failed`

### 11.8 Replay and Verification

A D2D implementation MUST reject replayed messages.

The reference implementation uses `senderDID|messageID` replay tracking and verifies inbound signatures against resolved verification methods.

### 11.9 Federated Execution

This is where interaction learnings materially expand the protocol.

A remote Dina is not just a mailbox. It may act as an orchestrator.

Therefore the protocol MUST allow the following D2D fulfillment patterns:

- direct response
- remote query
- remote delegated execution
- remote monitoring or subscription

Example:

- your Dina asks a transit operator Dina for a bus ETA
- the transit Dina may query its own database, call traffic systems, or invoke its own task executor
- your Dina receives a protocol-level outcome, not the transit Dina's internal implementation details

For this reason, D2D interop SHOULD standardize a generic request-state plane.

Recommended request modes are:

- `inline_preferred`
- `async`
- `subscription`

Recommended lifecycle states are:

- `accepted`
- `waiting_approval`
- `waiting_input`
- `running`
- `partial`
- `completed`
- `failed`
- `cancelled`
- `expired`

Required fields in a generic D2D request-state object SHOULD include:

- `request_id`
- `correlation_id`
- `requested_capability`
- `response_mode`
- `status`
- `created_at`
- `updated_at`

Optional fields SHOULD include:

- `result`
- `error`
- `retryable`
- `progress`
- `percent`
- `eta`
- `expires_at`
- `provenance`
- `requires_human`
- `next_action`

Support for request modes, lifecycle states, and optional fields SHOULD be advertised through the capability discovery layer so that remote peers know the highest common lifecycle they can use.

The current v1 transport families do not yet fully freeze this generic request-state plane. That is an open standardization task before Protocol 1.0.

Draft machine-readable artifacts for D2D interop live in:

- `schemas/d2d-envelope.json`
- `schemas/capability-advertisement.json`
- `schemas/capability-negotiation.json`

## 12. Layer 6: Trust Network

### 12.1 Purpose

Trust defines how Dina-compatible systems publish verifiable outcomes and query world trust.

### 12.2 Protocol Requirements

A Dina-compatible trust layer MUST support:

- attributable signed trust records
- public indexing
- retraction or revocation
- subject resolution
- queryable trust profiles

### 12.3 Write Path

The intended Dina trust write model is:

- Home Nodes publish signed records to an AT Protocol PDS
- a relay and indexer ingest those records
- AppViews serve read-side query results

The current reference implementation already has:

- PDS publishing through XRPC
- `did:plc`-backed repo writes
- AppView-oriented query handlers

### 12.4 Collection Namespace

The protocol trust namespace is `com.dina.trust.*`.

The current broader design already includes a large set of collections, including families such as:

- attestation
- vouch
- endorsement
- flag
- reply
- reaction
- reportRecord
- revocation
- delegation
- collection
- media
- subject
- amendment
- verification
- reviewRequest
- comparison
- subjectClaim
- trustPolicy
- notificationPrefs

The protocol MUST freeze at least a minimal required subset for 1.0.

Recommended minimal core subset:

- `com.dina.trust.attestation`
- `com.dina.trust.vouch`
- `com.dina.trust.flag`
- `com.dina.trust.revocation`
- `com.dina.trust.verification`

### 12.5 Read Contract

A Dina-compatible AppView SHOULD expose read-side queries for:

- resolve profile
- search trust
- fetch attestations
- fetch graph or neighborhood

The reference implementation already exposes local Home Node handlers that proxy profile resolve and search to an AppView.

### 12.6 Trust Score Semantics

The protocol MUST separate:

- record interoperability
- score algorithm interoperability

Write interoperability requires a stable record namespace and shape.

Read interoperability requires that a trust profile can at least expose:

- trust score
- confidence
- ring or classification
- evidence summary
- active flags or warnings

Exact scoring algorithms, including EigenTrust-style convergence, SHOULD be published as profile definitions rather than assumed to be globally frozen at the protocol core.

### 12.7 Privacy Rule

The Trust Network MUST NOT become a backdoor for private vault disclosure.

Only explicitly publishable, signed, public trust records belong in the trust layer.

## 13. Layer 7: Interaction Contract

### 13.1 Purpose

This is the missing interoperability layer most systems ignore.

Without it, two implementations may share identity, auth, D2D, and trust but still fail to behave similarly from the user's point of view.

The Interaction Contract layer standardizes:

- what a user-level operation means
- what a proactive Dina-to-human delivery means
- what result shape is expected
- how one external interaction may decompose internally
- how requests are grounded into structured context

### 13.2 Semantic Operation Registry

The protocol SHOULD standardize semantic operation types, not command spellings.

The registry itself is already part of the protocol design. The remaining 1.0 work is to freeze:

- the mandatory core subset
- extension rules for vendor- or profile-specific operations
- machine-readable schema for operation declaration and validation

Implementations SHOULD advertise their supported semantic operations through the capability discovery layer rather than forcing remote peers to infer them from product-specific UI or command names.

Recommended primary semantic operations are:

- `remember_context`
- `answer_now`
- `propose_plan`
- `communicate_person`
- `request_service`
- `delegate_task`
- `watch_condition`

Control operations:

- `approve_operation`
- `inspect_status`
- `modify_existing`
- `cancel_operation`
- `recover_operation`

Resource operations:

- `manage_contact`
- `manage_config`
- `manage_device`
- `manage_session`
- `inspect_audit`

Trust operations:

- `query_trust`
- `publish_review`
- `publish_vouch`
- `publish_flag`

Outbound delivery operations:

- `deliver_briefing`
- `deliver_alert`
- `deliver_update`
- `deliver_completion`
- `deliver_failure`
- `request_clarification`
- `escalate_attention`

These are protocol semantics, not command names.

One implementation might expose:

- `/ask`
- `/plan`
- `/send`
- `/request`

Another might expose:

- `/query`
- `/advise`
- `/message`
- `/service`

Another might expose no slash commands at all and use only buttons, forms, or plain language.

All of them remain interoperable if they preserve the same semantic contracts.

### 13.2A Outbound Delivery Semantics

The interaction protocol is not only human -> Dina.

It must also standardize when Dina speaks to the human without a fresh inbound command.

At minimum, a conforming implementation SHOULD support these proactive delivery semantics:

- `deliver_briefing`: aggregated, priority-ranked summary
- `deliver_alert`: immediate fiduciary or harm-preventing notice
- `deliver_update`: non-terminal progress or state change
- `deliver_completion`: solicited work reached a useful terminal point
- `deliver_failure`: solicited work failed or became invalid
- `request_clarification`: Dina cannot proceed safely without more input
- `escalate_attention`: a previously lower-priority item now requires interruption

These are semantic contracts, not UI prescriptions.

One implementation may show a push notification.
Another may add a notification center item.
Another may surface the item only in a daily briefing.

### 13.3 Reference Mapping

The current Dina reference product maps its external command surface approximately as follows:

- `/remember` -> `remember_context`
- `/ask` -> `answer_now`
- `/plan` -> `propose_plan`
- `/send` -> `communicate_person`
- `/request` -> `request_service`
- `/task` -> `delegate_task`
- `/watch` -> `watch_condition`
- `/approve` -> `approve_operation`
- `/status` -> `inspect_status`
- `/edit` -> `modify_existing`
- `/cancel` -> `cancel_operation`
- `/recover` -> `recover_operation`

Resource examples:

- `/contact ...` -> `manage_contact`
- `/config ...` -> `manage_config`
- `/device ...` -> `manage_device`
- `/session ...` -> `manage_session`
- `/audit ...` -> `inspect_audit`

Trust examples:

- `/trust` -> `query_trust`
- `/review` -> `publish_review`
- `/vouch` -> `publish_vouch`
- `/flag` -> `publish_flag`

This mapping is explicitly non-normative.

### 13.4 Outer Contract vs Execution Graph

This is the key interaction rule:

- the external operation defines the outer contract
- the implementation is free to build the internal execution graph

Examples:

- an `answer_now` operation may internally decompose into `answer_now + request_service + propose_plan + context_resolution`
- a `propose_plan` operation may internally decompose into `answer_now + compare + synthesize`
- a `delegate_task` operation may internally decompose into `propose_plan + answer_now + request_service + watch_condition`
- a `watch_condition` operation may internally decompose into `request_service + subscribe + status_tracking`

This rule is required to handle real scenarios such as:

- asking a remote transit Dina for a bus ETA
- planning a commute from live traffic and calendar data
- delegating long-running research to a remote executor
- monitoring a service or delivery and alerting later

### 13.4A Multi-Turn Interaction Frames

The protocol SHOULD model interactions as frames, not isolated stateless turns.

An interaction frame SHOULD carry at least:

- current outer contract
- resolved entities and context
- pending clarification
- proposed options or candidate actions
- any active approval or execution state

Follow-up turns SHOULD remain within the existing outer contract unless the user clearly pivots.

Examples:

- disambiguation remains inside the current contract
- correction remains inside the current contract
- selecting an option from a prior plan starts a new execution contract with the plan retained as provenance

### 13.5 Counterparty Classes

The interaction protocol SHOULD distinguish:

- person-oriented communication
- provider or institution request
- infrastructure request
- device request
- delegated executor request

This distinction matters because:

- `communicate_person` is different from `request_service`
- a D2D social exchange is different from a provider action
- a remote Dina may be social, institutional, or infrastructural

### 13.5A Persona and Sensitivity Scope

The interaction protocol SHOULD treat persona scope as part of routing, not merely storage.

At minimum, a conforming implementation SHOULD evaluate:

- target persona or personas
- persona tier
- interaction sensitivity
- whether the request crosses persona boundaries
- whether approval, grant, or explicit unlock is required

This is necessary for interoperable governance because:

- remembering into health is not governed like remembering into general
- a finance request is not governed like a restaurant booking
- a plan spanning multiple personas may require explicit cross-persona consent

### 13.6 Context Resolution

Before fulfillment, a Dina-compatible implementation SHOULD resolve:

- subject context
- spatial context
- temporal context
- conversation context
- relationship context
- policy context
- confidence

This is what allows phrases like:

- "this bus"
- "here"
- "them"
- "my usual route"

to become structured interoperable requests.

### 13.6A Delivery and Notification Contract

The interaction protocol SHOULD also standardize how work completion and failure are surfaced back to the human.

At minimum, implementations should distinguish:

- inline response
- queued completion notice
- proactive failure notice
- briefing inclusion
- escalation due to harm if ignored

This is the outbound half of Silence First.

### 13.7 Response Contract

The response layer SHOULD support a portable envelope with a small, typed set of response kinds:

- `summary`
- `list`
- `table`
- `comparison`
- `status`
- `error`
- `raw`

This is not just UI polish. It is part of protocol interoperability because agents, Dinas, bridges, and clients need a stable result contract.

At minimum, the response envelope SHOULD freeze these core fields:

- `schema_version`
- `type`
- `title`
- `text`
- `data`
- `meta`

The response layer SHOULD also remain explicitly extensible. A conforming envelope MAY include:

- `annotations`
- `disclosures`
- `extensions`

This is important because different Dina-compatible ecosystems may allow different downstream behaviors while still remaining interoperable.

Examples:

- provenance annotations
- trust or confidence annotations
- sponsorship or commercial disclosures
- implementation-specific rendering hints

The protocol should require that such additions be explicit and machine-readable rather than hidden inside arbitrary text.

A draft machine-readable schema for this contract lives in:

- `schemas/response-envelope.json`

Implementations SHOULD advertise supported response kinds, envelope versions, and extension namespaces through the capability discovery layer.

### 13.8 Bridges vs Native D2D

Email, chat apps, and provider APIs remain important, but they are not the same thing as Dina-to-Dina communication.

The interaction protocol therefore distinguishes:

- native D2D coordination
- provider / infrastructure requests
- bridge delivery over email or chat

This preserves the qualitative difference between:

- person-to-person sovereign coordination
- formal correspondence
- service requests

without requiring every implementation to expose those differences through the same UI.

## 14. Conformance Profiles

Not every implementation must implement every layer.

The protocol SHOULD define conformance profiles.

Implementations SHOULD advertise their supported profile identifiers through the capability discovery layer.

### 14.1 Home Node Profile

Implements:

- Identity
- Authentication
- Encrypted Storage
- Agent Safety
- D2D
- Trust publishing or proxying
- Interaction Contract

This is the fullest Dina implementation.

### 14.2 Device Profile

Implements:

- device identity
- request signing
- pairing
- selected interaction operations

May delegate storage and D2D authority to a Home Node.

### 14.3 Agent Executor Profile

Implements:

- Authentication
- Agent Safety contract
- selected interaction semantics

May act only through a Home Node and does not own Dina identity or vault authority.

### 14.3A Agent Compliance Model

The protocol SHOULD be explicit about what it means for an agent to be Dina-protocol-compliant.

Not every agent that can call a Dina endpoint is Dina-protocol-compliant.

At minimum, the protocol distinguishes:

1. Dina-compatible agent executor
   - implements the `agent-executor` profile
   - authenticates using Dina-compatible auth
   - declares intent and caller identity
   - obeys session, grant, approval, and egress controls
   - consumes or produces supported interaction semantics through a Home Node

2. Dina-aware agent
   - can talk to a Dina-compatible Home Node or bridge
   - but does not itself implement the required executor profile contract
   - is integration-capable, not protocol-conformant

3. Sovereign Dina implementation
   - implements a fuller profile such as `home-node`, `provider-dina`, or `mobile-home-node`
   - may also host or route work to one or more compliant agent executors

An agent therefore SHOULD NOT be described simply as \"Dina-compatible\" without a profile qualifier.

Recommended language is:

- \"Dina-compatible agent executor\"
- \"Dina-aware external agent\"
- \"Dina-compatible Home Node\"

This avoids conflating:

- an executor that works under a Home Node's authority
- a sovereign Dina implementation
- a third-party tool integration that merely speaks some Dina-adjacent API

### 14.3B Minimum Agent Executor Requirements

To claim the `agent-executor` profile, an agent implementation SHOULD at minimum:

- advertise the `agent-executor` profile through capability discovery
- support Dina-compatible authentication
- propagate caller context containing principal, actor, origin, and auth mode
- declare semantic operations it can participate in
- honor session and grant boundaries
- surface approval-required actions instead of bypassing them
- preserve portable response-envelope semantics for the results it returns
- avoid claiming storage, D2D, or trust capabilities it does not implement

An agent executor MAY be narrow.

For example, an implementation may legitimately support only:

- `delegate_task`
- `answer_now`
- `request_service`
- `status`

provided it advertises that subset honestly and negotiates against the remote common subset.

### 14.4 D2D Service / Provider Profile

Implements:

- Identity
- Authentication
- D2D endpoint or request endpoint
- selected interaction semantics

May satisfy requests through direct lookup, internal tools, or delegated execution.

### 14.5 Trust AppView Profile

Implements:

- Trust indexing
- trust query endpoints

Does not need to implement vault storage or agent safety.

### 14.6 Bridge Connector Profile

Implements:

- Authentication to a Home Node
- selected `communicate_person`, `request_service`, and `watch_condition` semantics
- delivery over non-native channels such as email or chat

This preserves interoperability without making bridges the protocol center.

### 14.7 Mobile Home Node Profile

Implements:

- Identity
- Authentication
- Encrypted Storage
- Agent Safety
- D2D (via mailbox relay)
- Trust querying (via hosted AppView)
- Interaction Contract

Delegates to hosted infrastructure:

- mailbox relay for offline D2D delivery
- AppView for Trust Network queries
- PDS for trust record publishing

This profile enables zero-cost sovereign operation
on a mobile device with no user-managed server.

## 15. Scenario Coverage

The protocol should be judged against scenario families, not only transport primitives.

### 15.1 Personal Memory

Pattern:

- remember_context
- encrypted storage
- optional approvals
- later `answer_now` or `propose_plan`

### 15.2 Live World-State Query

Pattern:

- answer_now
- context resolution
- provider or infrastructure request
- possible D2D or remote execution
- typed status response

Example:

- "What time will the bus reach here?"

### 15.3 Social Coordination

Pattern:

- communicate_person
- D2D preferred
- sharing policy
- approval if needed

Example:

- "Tell Sancho I'll be 20 minutes late."

### 15.4 Provider Request

Pattern:

- request_service
- provider Dina or API
- possible remote task or federated execution
- status or final result

Example:

- "Ask the clinic whether the doctor is running late."

### 15.5 Durable Delegated Work

Pattern:

- delegate_task
- executor routing
- async lifecycle
- callback or polling
- final structured result

### 15.6 Monitoring

Pattern:

- watch_condition
- subscription or polling
- later notification

Example:

- "Watch this bus and tell me when it is 5 minutes away."

### 15.6A Briefing and Proactive Delivery

Pattern:

- `deliver_briefing`
- `deliver_alert`
- `deliver_completion`
- `deliver_failure`
- priority-ranked outbound delivery

Examples:

- morning briefing
- failed task that should not stay silent
- urgent reminder that became fiduciary

### 15.7 Trust Query

Pattern:

- `answer_now` or `propose_plan`
- trust lookup via AppView
- evidence-aware result

Example:

- "Show me only items with verified provenance."

### 15.8 Trust Publication

Pattern:

- `publish_review`, `publish_vouch`, or `publish_flag`
- signed public record
- AppView indexing
- retractable lifecycle

## 16. Security Properties

At protocol level, Dina aims to guarantee:

- sovereign identity under user-controlled root material
- request authenticity via Ed25519
- replay protection
- encryption at rest per persona
- explicit safety mediation for risky agent access
- strong D2D confidentiality and authenticity
- attributable public trust publication
- portable interaction semantics above transport

## 17. Open Standardization Questions

### 17.1 Core Freeze Points

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

### 17.2 Profile and Portability Questions

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

## 18. What "Dina as Protocol" Means

When this protocol is implemented correctly:

- a Rust Home Node can interoperate with a Go Home Node
- a Swift device can sign requests against any Dina-compatible node
- a browser bridge can participate in interaction semantics without owning vault authority
- one implementation can discover another implementation's supported profiles and extensions before attempting optional interop
- one Dina can ask another Dina for an outcome, and the remote Dina can satisfy it through its own tools or executors
- any AppView that indexes `com.dina.trust.*` can serve trust for any Dina-compatible identity
- vaults, trust records, D2D messages, and interaction semantics stop being implementation-private details

That is the transition from product to infrastructure.

## 19. Reference Implementation Status

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
