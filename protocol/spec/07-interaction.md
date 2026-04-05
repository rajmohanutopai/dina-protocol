# 07 Interaction

This chapter defines semantic operations, outbound delivery, execution decomposition, context resolution, and response contracts.



## Purpose

This is the missing interoperability layer most systems ignore.

Without it, two implementations may share identity, auth, D2D, and trust but still fail to behave similarly from the user's point of view.

The Interaction Contract layer standardizes:

- what a user-level operation means
- what a proactive Dina-to-human delivery means
- what result shape is expected
- how one external interaction may decompose internally
- how requests are grounded into structured context

## Semantic Operation Registry

The protocol SHOULD standardize semantic operation types, not command spellings.

The registry itself is already part of the protocol design. The remaining 1.0 work is to freeze:

- the mandatory core subset
- extension rules for vendor- or profile-specific operations
- machine-readable schema for operation declaration and validation

Implementations SHOULD advertise their supported semantic operations through the capability discovery layer rather than forcing remote peers to infer them from product-specific UI or command names.

Recommended primary semantic operations are:

- `remember_context`
- `answer_now`
- `propose_plan`
- `communicate_person`
- `request_service`
- `delegate_task`
- `watch_condition`

Control operations:

- `approve_operation`
- `inspect_status`
- `modify_existing`
- `cancel_operation`
- `recover_operation`

Resource operations:

- `manage_contact`
- `manage_config`
- `manage_device`
- `manage_session`
- `inspect_audit`

Trust operations:

- `query_trust`
- `publish_review`
- `publish_vouch`
- `publish_flag`

Outbound delivery operations:

- `deliver_briefing`
- `deliver_alert`
- `deliver_update`
- `deliver_completion`
- `deliver_failure`
- `request_clarification`
- `escalate_attention`

These are protocol semantics, not command names.

One implementation might expose:

- `/ask`
- `/plan`
- `/send`
- `/request`

Another might expose:

- `/query`
- `/advise`
- `/message`
- `/service`

Another might expose no slash commands at all and use only buttons, forms, or plain language.

All of them remain interoperable if they preserve the same semantic contracts.

## Outbound Delivery Semantics

The interaction protocol is not only human -> Dina.

It must also standardize when Dina speaks to the human without a fresh inbound command.

At minimum, a conforming implementation SHOULD support these proactive delivery semantics:

- `deliver_briefing`: aggregated, priority-ranked summary
- `deliver_alert`: immediate fiduciary or harm-preventing notice
- `deliver_update`: non-terminal progress or state change
- `deliver_completion`: solicited work reached a useful terminal point
- `deliver_failure`: solicited work failed or became invalid
- `request_clarification`: Dina cannot proceed safely without more input
- `escalate_attention`: a previously lower-priority item now requires interruption

These are semantic contracts, not UI prescriptions.

One implementation may show a push notification.
Another may add a notification center item.
Another may surface the item only in a daily briefing.

## Reference Mapping

The current Dina reference product maps its external command surface approximately as follows:

- `/remember` -> `remember_context`
- `/ask` -> `answer_now`
- `/plan` -> `propose_plan`
- `/send` -> `communicate_person`
- `/request` -> `request_service`
- `/task` -> `delegate_task`
- `/watch` -> `watch_condition`
- `/approve` -> `approve_operation`
- `/status` -> `inspect_status`
- `/edit` -> `modify_existing`
- `/cancel` -> `cancel_operation`
- `/recover` -> `recover_operation`

Resource examples:

- `/contact ...` -> `manage_contact`
- `/config ...` -> `manage_config`
- `/device ...` -> `manage_device`
- `/session ...` -> `manage_session`
- `/audit ...` -> `inspect_audit`

Trust examples:

- `/trust` -> `query_trust`
- `/review` -> `publish_review`
- `/vouch` -> `publish_vouch`
- `/flag` -> `publish_flag`

This mapping is explicitly non-normative.

## Outer Contract vs Execution Graph

This is the key interaction rule:

- the external operation defines the outer contract
- the implementation is free to build the internal execution graph

Examples:

- an `answer_now` operation may internally decompose into `answer_now + request_service + propose_plan + context_resolution`
- a `propose_plan` operation may internally decompose into `answer_now + compare + synthesize`
- a `delegate_task` operation may internally decompose into `propose_plan + answer_now + request_service + watch_condition`
- a `watch_condition` operation may internally decompose into `request_service + subscribe + status_tracking`

This rule is required to handle real scenarios such as:

- asking a remote transit Dina for a bus ETA
- planning a commute from live traffic and calendar data
- delegating long-running research to a remote executor
- monitoring a service or delivery and alerting later

## Multi-Turn Interaction Frames

The protocol SHOULD model interactions as frames, not isolated stateless turns.

An interaction frame SHOULD carry at least:

- current outer contract
- resolved entities and context
- pending clarification
- proposed options or candidate actions
- any active approval or execution state

Follow-up turns SHOULD remain within the existing outer contract unless the user clearly pivots.

Examples:

- disambiguation remains inside the current contract
- correction remains inside the current contract
- selecting an option from a prior plan starts a new execution contract with the plan retained as provenance

## Counterparty Classes

The interaction protocol SHOULD distinguish:

- person-oriented communication
- provider or institution request
- infrastructure request
- device request
- delegated executor request

This distinction matters because:

- `communicate_person` is different from `request_service`
- a D2D social exchange is different from a provider action
- a remote Dina may be social, institutional, or infrastructural

## Persona and Sensitivity Scope

The interaction protocol SHOULD treat persona scope as part of routing, not merely storage.

At minimum, a conforming implementation SHOULD evaluate:

- target persona or personas
- persona tier
- interaction sensitivity
- whether the request crosses persona boundaries
- whether approval, grant, or explicit unlock is required

This is necessary for interoperable governance because:

- remembering into health is not governed like remembering into general
- a finance request is not governed like a restaurant booking
- a plan spanning multiple personas may require explicit cross-persona consent

## Context Resolution

Before fulfillment, a Dina-compatible implementation SHOULD resolve:

- subject context
- spatial context
- temporal context
- conversation context
- relationship context
- policy context
- confidence

This is what allows phrases like:

- "this bus"
- "here"
- "them"
- "my usual route"

to become structured interoperable requests.

## Delivery and Notification Contract

The interaction protocol SHOULD also standardize how work completion and failure are surfaced back to the human.

At minimum, implementations should distinguish:

- inline response
- queued completion notice
- proactive failure notice
- briefing inclusion
- escalation due to harm if ignored

This is the outbound half of Silence First.

## Response Contract

The response layer SHOULD support a portable envelope with a small, typed set of response kinds:

- `summary`
- `list`
- `table`
- `comparison`
- `status`
- `error`
- `raw`

This is not just UI polish. It is part of protocol interoperability because agents, Dinas, bridges, and clients need a stable result contract.

At minimum, the response envelope SHOULD freeze these core fields:

- `schema_version`
- `type`
- `title`
- `text`
- `data`
- `meta`

The response layer SHOULD also remain explicitly extensible. A conforming envelope MAY include:

- `annotations`
- `disclosures`
- `extensions`

This is important because different Dina-compatible ecosystems may allow different downstream behaviors while still remaining interoperable.

Examples:

- provenance annotations
- trust or confidence annotations
- sponsorship or commercial disclosures
- implementation-specific rendering hints

The protocol should require that such additions be explicit and machine-readable rather than hidden inside arbitrary text.

A draft machine-readable schema for this contract lives in:

- `schemas/response-envelope.json`

Implementations SHOULD advertise supported response kinds, envelope versions, and extension namespaces through the capability discovery layer.

## Bridges vs Native D2D

Email, chat apps, and provider APIs remain important, but they are not the same thing as Dina-to-Dina communication.

The interaction protocol therefore distinguishes:

- native D2D coordination
- provider / infrastructure requests
- bridge delivery over email or chat

This preserves the qualitative difference between:

- person-to-person sovereign coordination
- formal correspondence
- service requests

without requiring every implementation to expose those differences through the same UI.

