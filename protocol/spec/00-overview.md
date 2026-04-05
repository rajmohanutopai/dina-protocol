# 00 Overview

This chapter defines the shared protocol frame that all Dina-compatible implementations build on.

## Scope

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

## Design Goals

The protocol exists so that:

- a Go, Rust, Swift, Python, or browser-based implementation can interoperate
- a managed hosted Dina and a self-hosted Dina can still speak the same contracts
- a specialist agent can plug into Dina without owning Dina's identity or policy layer
- a D2D request can be fulfilled by a remote Dina regardless of its internal executor
- the Trust Network remains portable across AppViews and Home Nodes
- user interaction semantics remain stable even when implementations differ internally
- implementations can advertise what they support before attempting advanced interop
- optional features can evolve without fragmenting the core protocol

## Relationship to the Interaction Architecture

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

## Conformance Language

The keywords MUST, SHOULD, MAY, MUST NOT, and SHOULD NOT are used in the RFC sense.

This document distinguishes:

- Protocol contract: what any conforming implementation must do
- Reference behavior: what the current Dina implementation does today
- Open freeze point: a place where the reference implementation still needs one protocol choice to be standardized before a 1.0 release
- Core protocol: the minimum frozen subset every conforming implementation shares
- Profile: a named, optional capability bundle built on top of the core protocol
- Extension: a namespaced optional feature that may be ignored when unsupported
- Profile-qualified compliance: an implementation is Dina-protocol-compliant only with respect to one or more declared profiles, not by vague association with Dina

## Protocol Shape

The Dina protocol should be strict only where interoperability actually requires strictness.

Its shape is:

- minimal frozen core
- named conformance profiles
- namespaced extensions
- capability discovery and negotiation
- layered interoperability contracts

### Minimal Frozen Core

The core protocol should freeze only the pieces that must be identical across implementations:

- identity derivation
- signed request authentication
- minimum D2D envelope and lifecycle semantics
- minimum trust namespace subset
- minimum semantic operation set
- minimum response envelope core

Everything else should remain profile-based or extensible.

### Profiles

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

### Extensions and Namespacing

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

### Capability Discovery and Negotiation

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

### Protocol Layers

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

## Core Entities

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

