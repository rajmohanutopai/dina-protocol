# Dina Protocol Test Matrix

Status: draft

This matrix defines the minimum interoperability checks a Dina-compatible implementation should pass.

## 1. Core Conformance Matrix

| ID | Area | Check | Expected Result |
|---|---|---|---|
| C1 | Identity | Restore the same mnemonic in two implementations | Same canonical root key material and same derived signing keys |
| C2 | Identity | Resolve a published DID document | Same verification methods and service endpoints are understood |
| C3 | Authentication | Verify the canonical signed payload | Receiver validates signature, timestamp, and nonce correctly |
| C4 | Authentication | Reject replayed request | Second use of same nonce/signature is rejected |
| C5 | Storage | Export logical vault data from implementation A and import into B | Records, provenance, trust fields, and persona metadata survive logically intact |
| C6 | Agent Safety | Submit portable approval request | Receiver can render and process approval state correctly |
| C7 | D2D | Send a basic `coordination.request` envelope | Receiver parses, authenticates, and routes the envelope |
| C8 | D2D | Execute generic request-state lifecycle | Both sides agree on request mode, status transitions, and required fields |
| C9 | Trust | Publish one mandatory trust record from one implementation and query from another | AppView/consumer sees the record under the correct core namespace |
| C10 | Interaction | Map a local UI command to `answer_now` | Receiver or local runtime preserves semantic operation contract |
| C11 | Interaction | Parse portable response envelope | Client renders the same typed result shape |
| C12 | Capability | Fetch capability advertisement | Remote peer understands supported versions, profiles, and limits |
| C13 | Capability | Negotiate a common subset | Interaction uses selected common subset rather than failing implicitly |
| C14 | Agent Compliance | Verify an implementation claiming `agent-executor` profile | It authenticates, declares intent, and operates only within granted session scope |

## 2. Required Core Freeze-Point Tests

| Freeze Point | Test | Pass Condition |
|---|---|---|
| Root key material profile | Generate mnemonic and compare downstream key derivation across implementations | Same canonical root key material and same derived root signing key |
| D2D request-state plane | Execute `inline_preferred`, `async`, and `subscription` requests | Both sides agree on request mode, lifecycle transitions, and timestamps |
| Trust core subset | Publish/query attestation, vouch, flag, revocation, verification | All five core collections interoperate |
| Capability discovery and negotiation | Exchange advertisement and live negotiation | Selected protocol subset is explicit and enforceable |
| Interaction operation registry | Execute core operations against one implementation from another | Semantic contract is preserved independent of UI command spelling |
| Response envelope schema | Exchange all seven core response kinds | Receiver accepts and renders all supported core types |

## 3. Profile Tests

| Profile | Check | Expected Result |
|---|---|---|
| Home Node | Full core stack plus trust publish/query and D2D | End-to-end interop succeeds |
| Device | Pair, sign, and operate through Home Node | Device can authenticate and invoke core operations |
| Agent Executor | Consume delegated work without owning identity or policy | Executor acts within granted scope only |
| Provider Dina | Accept `request_service` and return provider result | Service-facing interop works with correct lifecycle |
| Trust AppView | Index and serve trust records | Read-side trust interop works |
| Bridge | Map non-native channel into Dina interaction contract | Bridge preserves semantic operation and disclosure state |
| Mobile Home Node | Operate through relay/AppView-backed profile | Mobile profile remains interoperable despite partial delegation |
| Encrypted Vault Portability | Import encrypted vault file directly | Only applicable if both sides claim same encrypted-vault profile |

## 4. Scenario Interoperability Tests

| ID | Scenario | Semantic Operations | Pass Condition |
|---|---|---|---|
| S1 | Personal memory | `remember_context`, `answer_now` | Memory stored in one implementation can be exported/imported and later queried correctly |
| S2 | Bus ETA | `answer_now` + `request_service` | Caller receives typed status response after provider-side execution |
| S3 | Social coordination | `communicate_person` | Message reaches remote Dina or explicit fallback path |
| S4 | Provider request | `request_service` | Provider Dina can accept, run, and return result or failure state |
| S5 | Durable task | `delegate_task` | Async task lifecycle is inspectable and terminal result is portable |
| S6 | Monitoring | `watch_condition` | Watch can be created, updated, completed, cancelled, and failed explicitly |
| S7 | Briefing/failure delivery | `deliver_briefing`, `deliver_failure` | Dina-initiated delivery is surfaced without ambiguity |
| S8 | Trust query | `answer_now` or `propose_plan` + trust read | Evidence-aware trust result is rendered portably |
| S9 | Agent-mediated execution | `delegate_task` through a compliant executor | Home Node retains authority while executor remains profile-scoped |

## 5. Negative and Fallback Tests

| ID | Case | Expected Result |
|---|---|---|
| N1 | Unsupported optional extension | Receiver ignores or declines extension explicitly; core interaction still proceeds if possible |
| N2 | Unsupported profile | Negotiation falls back to common subset or rejects clearly |
| N3 | Unknown response extension | Client still renders core response fields |
| N4 | Missing required D2D lifecycle field | Receiver rejects as invalid protocol payload |
| N5 | Expired approval request | Action is blocked until renewed or replaced |
| N6 | Replay of signed request or D2D message | Receiver rejects replay and records audit event |
| N7 | Agent claims `agent-executor` but bypasses approval or scope | Implementation is not conformant with the executor profile |

## 6. Release Gate For Protocol 1.0

Before calling Dina Protocol 1.0 stable, at least these should be true:

1. All six core freeze-point tests pass across at least two independent implementations.
2. Capability advertisement and negotiation are machine-readable and stable.
3. The seven core response kinds are accepted and rendered portably.
4. The five mandatory trust collections interoperate through at least one AppView implementation.
5. One provider-style D2D request and one delegated-task flow work end to end across implementation boundaries.
