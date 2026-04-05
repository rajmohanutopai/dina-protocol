# Agent Executor Profile

Status: draft

This note explains what it means for an agent to claim Dina-protocol compliance as an executor.

## 1. Purpose

The Dina protocol distinguishes between:

- sovereign Dina implementations
- compliant agent executors operating under a Home Node
- external agents that only integrate with Dina loosely

This document is for the middle case.

## 2. What An Agent Executor Is

A Dina-compatible agent executor is not a sovereign Dina.

It does not own:
- Home Node identity
- vault authority
- trust publication authority
- independent D2D authority unless separately implemented and advertised

It does:
- authenticate to a Home Node or equivalent sovereign node
- declare intent before acting
- obey session, grant, approval, and egress controls
- return portable structured results
- truthfully advertise its supported protocol subset

## 3. What It Is Not

An agent executor is not compliant merely because it can:
- call one HTTP endpoint
- read one task payload
- post back a text result
- bypass approvals through an internal integration path

That is Dina-aware integration, not executor-profile conformance.

## 4. Minimum Compliance Requirements

To claim the `agent-executor` profile, an implementation should satisfy all of the following.

### 4.1 Identity and Authentication
- expose a stable DID or equivalent authenticated principal recognized by the Home Node
- sign requests using Dina-compatible authentication
- support replay-safe request semantics

### 4.2 Capability Advertisement
- advertise the `agent-executor` profile
- declare supported semantic operations honestly
- declare supported response-envelope versions and kinds
- declare limits and unsupported optional features explicitly

### 4.3 Intent Declaration
- declare intent before sensitive access or external action
- provide at least:
  - `agent_did`
  - `action`
  - `target`
  - `persona_id` or persona scope
  - `trust_level`
  - `constraints`
- surface intent in a form the Home Node can evaluate portably

### 4.4 Authority Boundaries
- operate only within granted session scope
- never self-grant broader access
- never bypass approval-required actions
- treat missing grant or denial as terminal for that action unless reauthorized

### 4.5 Egress and Safety
- honor PII scrubbing and redaction requirements
- do not exfiltrate sensitive vault content outside allowed channels
- respect policy decisions made by the Home Node

### 4.6 Result Contract
- return results using the portable response envelope
- preserve `status` and `error` semantics clearly
- expose provenance or disclosure annotations when relevant

### 4.7 Operational Honesty
- do not claim storage, trust, D2D, or proactive-delivery support if not implemented
- do not claim provider or sovereign capabilities when acting only as an executor

## 5. Narrow Executors Are Valid

An executor may support only a small subset of operations.

Example valid subset:
- `delegate_task`
- `answer_now`
- `request_service`
- `inspect_status`

That is acceptable if the subset is advertised clearly and negotiated honestly.

## 6. Relationship To The Home Node

The Home Node remains the sovereign authority.

The executor is subordinate in protocol terms.

The Home Node owns:
- identity
- vaults
- approvals
- trust decisions
- policy
- final authority on outbound action

The executor supplies:
- reasoning
- tool execution
- external lookups within granted scope
- structured outputs

## 7. Required Artifacts

An executor implementation should be able to point to:
- a capability advertisement
- an intent declaration schema or payload
- its supported response envelope version
- a conformance checklist against the executor profile

## 8. Suggested Validation Path

1. validate the executor's capability advertisement
2. validate its intent payloads
3. verify that approvals are surfaced rather than bypassed
4. verify that denied or expired scope blocks action
5. verify that returned results conform to the response envelope
6. verify that unsupported optional features are declined explicitly

## 9. Normative References In This Protocol Set

- [04-agent-safety.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/04-agent-safety.md)
- [08-profiles-and-scenarios.md](https://rajmohanutopai.github.io/dina-protocol/protocol/spec/08-profiles-and-scenarios.md)
- [agent-intent.json](https://rajmohanutopai.github.io/dina-protocol/protocol/schemas/agent-intent.json)
- [agent-executor-capability.example.json](https://rajmohanutopai.github.io/dina-protocol/protocol/examples/agent-executor-capability.example.json)
- [agent-executor-checklist.md](https://rajmohanutopai.github.io/dina-protocol/protocol/agent-executor-checklist.md)
