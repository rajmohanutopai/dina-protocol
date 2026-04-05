# 03 Storage

This chapter defines persona storage, logical portability, encrypted-at-rest behavior, and optional encrypted-vault portability.



## Purpose

Encrypted Storage defines how personal data is protected and made portable across implementations.

## Protocol Requirements

A Dina-compatible storage system MUST provide:

- per-persona isolation
- encryption at rest
- typed vault records
- provenance and trust fields
- staging / pending approval support
- import/export at the logical schema level

## Persona Model

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

## Physical Storage Profile

A Dina-compatible Home Node SHOULD use an encrypted SQL store per persona.

The reference implementation uses SQLCipher-backed SQLite databases, one per persona, with encrypted-at-rest FTS and embedding storage.

## Logical Vault Schema

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

## Search and Semantic Hydration

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

## Backup and Export

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

## Encrypted Vault Portability Profile

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

