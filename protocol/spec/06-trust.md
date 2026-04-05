# 06 Trust

This chapter defines the public trust layer, required record families, query semantics, and privacy boundaries.



## Purpose

Trust defines how Dina-compatible systems publish verifiable outcomes and query world trust.

## Protocol Requirements

A Dina-compatible trust layer MUST support:

- attributable signed trust records
- public indexing
- retraction or revocation
- subject resolution
- queryable trust profiles

## Write Path

The intended Dina trust write model is:

- Home Nodes publish signed records to an AT Protocol PDS
- a relay and indexer ingest those records
- AppViews serve read-side query results

The current reference implementation already has:

- PDS publishing through XRPC
- `did:plc`-backed repo writes
- AppView-oriented query handlers

## Collection Namespace

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

## Read Contract

A Dina-compatible AppView SHOULD expose read-side queries for:

- resolve profile
- search trust
- fetch attestations
- fetch graph or neighborhood

The reference implementation already exposes local Home Node handlers that proxy profile resolve and search to an AppView.

## Trust Score Semantics

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

## Privacy Rule

The Trust Network MUST NOT become a backdoor for private vault disclosure.

Only explicitly publishable, signed, public trust records belong in the trust layer.

