# Contract - Task Handoff Summary Events

## Metadata

| Field | Value |
|---|---|
| Feature | CAI-7974 |
| Interface | Task-scoped handoff summary events |
| Producer | `TaskManager` / task instance |
| Consumer | SDK consumer widget |
| Change type | new public SDK events |
| Status | draft - backend payload schema confirmation pending |

## Definition

The SDK consumes backend WebSocket messages and emits task-scoped public events for SDK consumers.

Consumed backend events:

| Event | Expected correlation | SDK behavior |
|---|---|---|
| `FEATURE_ENABLEMENT` | Agent/org or task-independent enablement context | Update summary enablement state if payload is valid. |
| `MID_CALL_SUMMARY` | `interactionId`, `conversationId`, or equivalent | Find matching task and emit public handoff summary event. |
| `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` | `interactionId`, `conversationId`, or equivalent | Find matching task and emit public response event or update documented response path. |

Public SDK events, final names to be confirmed during implementation:

| Event | Payload |
|---|---|
| `task:midCallSummary` | Typed `MidCallSummaryPayload` containing task correlation id, summary content, and metadata. |
| `task:midCallSummaryResponseSubsequentAgent` | Typed response payload containing task correlation id, action/status, and backend metadata. |

## Error / Failure Catalog

| Failure | SDK behavior |
|---|---|
| Unknown task correlation id | Ignore safely; do not create a new task. |
| Malformed payload | Ignore safely; log sanitized warning if useful. |
| Duplicate payload | Prefer de-duplication by backend message id if supplied; otherwise document best-effort WebSocket delivery. |
| Summary payload contains sensitive text | Emit to consumer event but never log raw body. |

## Delivery & Ordering Guarantees

- Delivery is best-effort in WebSocket receive order.
- SDK does not persist or replay missed summary events.
- Public event is task-scoped and should only emit for the matching task instance.

## Backward Compatibility

- Additive events only.
- Existing `TASK_EVENTS` and transfer/consult events must retain current names and behavior.
- Consumers that do not subscribe to new events see no behavior change.

## Security / Privacy

- Summary payload is consumer-visible data but log-restricted.
- Tests must prove logger/metrics calls do not include summary body text.
