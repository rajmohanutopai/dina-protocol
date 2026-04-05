# Agent Executor Conformance Checklist

Status: draft

Use this checklist when evaluating whether an implementation can claim the `agent-executor` Dina protocol profile.

## 1. Identity and Auth
- [ ] Executor has a stable authenticated principal, typically a DID.
- [ ] Executor signs requests using Dina-compatible auth.
- [ ] Executor handles nonce/timestamp/replay rules correctly.
- [ ] Executor propagates caller context: principal, actor, origin, auth mode.

## 2. Capability Advertisement
- [ ] Executor advertises the `agent-executor` profile.
- [ ] Executor declares supported semantic operations.
- [ ] Executor declares supported response-envelope versions and kinds.
- [ ] Executor declares unsupported optional features honestly.
- [ ] Executor advertises limits such as payload and timeout bounds.

## 3. Intent Contract
- [ ] Executor emits a portable agent-intent declaration before sensitive access or action.
- [ ] Intent includes `agent_did`.
- [ ] Intent includes `action`.
- [ ] Intent includes `target`.
- [ ] Intent includes persona scope.
- [ ] Intent includes trust/sensitivity or equivalent policy context.
- [ ] Intent includes constraints.

## 4. Session and Grant Boundaries
- [ ] Executor only operates within an active session.
- [ ] Executor respects scoped grants.
- [ ] Missing or expired grants block the action.
- [ ] Executor does not widen granted scope on its own.

## 5. Approval Handling
- [ ] Approval-required actions are surfaced for approval.
- [ ] Executor does not bypass approval through alternate code paths.
- [ ] Denied approvals block the action cleanly.
- [ ] Approval state is auditable.

## 6. Egress and Safety
- [ ] Executor respects PII scrubbing requirements.
- [ ] Executor does not send sensitive data outside allowed channels.
- [ ] Executor follows Home Node policy decisions.
- [ ] Executor handles policy denials as protocol outcomes, not silent fallthrough.

## 7. Result Contract
- [ ] Executor returns results using the portable response envelope.
- [ ] Success, partial, and failure states are explicit.
- [ ] Provenance or disclosure annotations are included when relevant.
- [ ] Unknown optional response extensions do not break the executor.

## 8. Operational Honesty
- [ ] Executor does not claim Home Node authority.
- [ ] Executor does not claim trust publication/query support unless implemented.
- [ ] Executor does not claim native D2D support unless implemented.
- [ ] Executor does not claim storage portability support unless implemented.

## 9. Interoperability Checks
- [ ] Capability advertisement validates against the protocol schema.
- [ ] Agent-intent payload validates against the protocol schema.
- [ ] Response envelopes validate against the protocol schema.
- [ ] Negotiation falls back to common subset when optional capabilities differ.

## 10. Minimum Pass Condition

An executor should not claim `agent-executor` conformance unless all critical items above pass:
- identity/auth
- intent contract
- session/grant boundaries
- approval handling
- result contract
- operational honesty

This checklist operationalizes the protocol sections and the agent-specific rows in [test-matrix.md](https://rajmohanutopai.github.io/dina-protocol/protocol/test-matrix.md).
