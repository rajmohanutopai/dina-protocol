# 01 Identity

This chapter defines sovereign identity, derivation, DID representation, and recovery for Dina-compatible implementations.



## Purpose

Identity defines who a sovereign participant is and how other implementations recognize that participant.

## Protocol Requirements

A Dina-compatible identity system MUST define:

- a recoverable root secret
- deterministic key derivation rules
- a DID representation
- a DID document or equivalent resolution record
- a key rotation model
- service endpoint discovery

## Root Secret Model

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

## Current Reference Implementation

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

## DID Methods

A Dina-compatible Home Node SHOULD expose a `did:plc` identity.

Device and service principals MAY use:

- `did:key`
- `did:plc`
- `did:web`

The current reference implementation accepts all three DID prefixes at the domain layer, while using `did:plc` as the primary Home Node identity and `did:key` for CLI device identities.

## DID Document Requirements

A Dina-compatible DID document SHOULD expose:

- one or more Ed25519 verification methods in multikey format
- authentication references
- service endpoints for Dina messaging and other declared capabilities

The reference implementation currently uses:

- Ed25519 multikeys with the `0xed01` multicodec prefix
- a Dina messaging service endpoint, typically `DinaMsgBox`

## Rotation and Recovery

Home Node identities MUST support key rotation.

For `did:plc`, rotation SHOULD be driven through a recovery key distinct from the root signing key.

The current reference implementation derives PLC recovery keys from the dedicated `m/9999'/2'/...` subtree and supports PDS/PLC-backed DID lifecycle operations.

## Protocol Freeze Point

The reference implementation currently has an unresolved identity-root question:

- some code paths treat the root as raw 32-byte entropy rendered as a BIP-39 mnemonic
- some documentation and tests treat the root as the 64-byte BIP-39 PBKDF2-expanded seed

Before Dina Protocol 1.0 is frozen, the protocol MUST standardize one root key material profile for downstream derivation.

Recommended direction:

- recovery secret: 24-word BIP-39 mnemonic
- canonical root key material: BIP-39 PBKDF2-expanded seed
- deterministic subkey derivation from that canonical root key material

