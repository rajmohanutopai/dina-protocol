# 02 Authentication

This chapter defines request signing, pairing, service keys, and caller identity for Dina-compatible authentication.



## Purpose

Authentication defines how a participant proves who it is.

## Protocol Requirements

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

## HTTP Header Contract

A signed HTTP request SHOULD carry:

- `X-DID`
- `X-Timestamp`
- `X-Nonce`
- `X-Signature`

`X-Signature` is the Ed25519 signature over the canonical payload.

## Replay Protection

A conforming implementation MUST protect against replay.

The reference implementation currently enforces:

- a 5-minute clock skew window
- a per-request nonce
- a double-buffer nonce cache for O(1) rotation and replay rejection

## Device Pairing

A Dina-compatible Home Node SHOULD support device pairing as a bootstrap flow.

The reference implementation currently uses:

- a 6-digit pairing code
- 5-minute TTL
- single-use semantics
- a cryptographic secret behind the visible code

Pairing MAY result in either:

- a registered Ed25519 device key
- a paired bearer token for compatibility or bootstrap flows

## Service Keys

Internal or peer services MAY authenticate with distinct service Ed25519 keypairs.

This is already present in the reference implementation for Core-to-Brain and similar service paths.

## Auth Modes

The Dina protocol recognizes at least these auth modes:

- device_signature
- service_signature
- paired_token
- browser_session

The protocol SHOULD treat signature-based authentication as the primary interoperable profile.

Paired bearer tokens are acceptable as a bootstrap or compatibility profile, but SHOULD NOT be the normative long-term cross-implementation trust model.

## Caller Model

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

