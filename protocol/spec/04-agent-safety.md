# 04 Agent Safety

This chapter defines sessions, approvals, intent evaluation, PII controls, and audit requirements for agent interaction with Dina.



## Purpose

Agent Safety defines how agents interact with personal data and external systems without collapsing user sovereignty.

## Protocol Requirements

A Dina-compatible safety layer MUST provide:

- session-scoped access
- approval requests
- scoped grants
- intent evaluation
- egress controls
- PII scrubbing
- auditability

## Session and Grant Model

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

## Approval Model

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

## Intent Evaluation

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

## Safety Rules

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

## PII Scrubbing

A Dina-compatible implementation MUST support PII scrubbing before cloud or third-party egress.

The reference implementation currently provides:

- Tier 1: regex-based scrubbing for structured PII
- Tier 2: Brain-assisted structured or NER-assisted scrubbing

The protocol SHOULD allow additional tiers, but MUST preserve the contract that scrubbed text can be safely passed onward and later rehydrated only within an authorized local context.

## Audit

Every approval, denial, sensitive access, or denied egress SHOULD be auditable.

This is already present in the reference implementation and should remain a required interoperability expectation for serious implementations.

