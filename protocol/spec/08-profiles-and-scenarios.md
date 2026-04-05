# 08 Profiles And Scenarios

This chapter defines conformance profiles and scenario coverage for evaluating Dina-compatible implementations.

## Conformance Profiles

Not every implementation must implement every layer.

The protocol SHOULD define conformance profiles.

Implementations SHOULD advertise their supported profile identifiers through the capability discovery layer.

## Home Node Profile

Implements:

- Identity
- Authentication
- Encrypted Storage
- Agent Safety
- D2D
- Trust publishing or proxying
- Interaction Contract

This is the fullest Dina implementation.

## Device Profile

Implements:

- device identity
- request signing
- pairing
- selected interaction operations

May delegate storage and D2D authority to a Home Node.

## Agent Executor Profile

Implements:

- Authentication
- Agent Safety contract
- selected interaction semantics

May act only through a Home Node and does not own Dina identity or vault authority.

## Agent Compliance Model

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

## Minimum Agent Executor Requirements

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

## D2D Service / Provider Profile

Implements:

- Identity
- Authentication
- D2D endpoint or request endpoint
- selected interaction semantics

May satisfy requests through direct lookup, internal tools, or delegated execution.

## Trust AppView Profile

Implements:

- Trust indexing
- trust query endpoints

Does not need to implement vault storage or agent safety.

## Bridge Connector Profile

Implements:

- Authentication to a Home Node
- selected `communicate_person`, `request_service`, and `watch_condition` semantics
- delivery over non-native channels such as email or chat

This preserves interoperability without making bridges the protocol center.

## Mobile Home Node Profile

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

## Scenario Coverage

The protocol should be judged against scenario families, not only transport primitives.

### Personal Memory

Pattern:

- remember_context
- encrypted storage
- optional approvals
- later `answer_now` or `propose_plan`

### Live World-State Query

Pattern:

- answer_now
- context resolution
- provider or infrastructure request
- possible D2D or remote execution
- typed status response

Example:

- "What time will the bus reach here?"

### Social Coordination

Pattern:

- communicate_person
- D2D preferred
- sharing policy
- approval if needed

Example:

- "Tell Sancho I'll be 20 minutes late."

### Provider Request

Pattern:

- request_service
- provider Dina or API
- possible remote task or federated execution
- status or final result

Example:

- "Ask the clinic whether the doctor is running late."

### Durable Delegated Work

Pattern:

- delegate_task
- executor routing
- async lifecycle
- callback or polling
- final structured result

### Monitoring

Pattern:

- watch_condition
- subscription or polling
- later notification

Example:

- "Watch this bus and tell me when it is 5 minutes away."

### Briefing and Proactive Delivery

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

### Trust Query

Pattern:

- `answer_now` or `propose_plan`
- trust lookup via AppView
- evidence-aware result

Example:

- "Show me only items with verified provenance."

### Trust Publication

Pattern:

- `publish_review`, `publish_vouch`, or `publish_flag`
- signed public record
- AppView indexing
- retractable lifecycle

