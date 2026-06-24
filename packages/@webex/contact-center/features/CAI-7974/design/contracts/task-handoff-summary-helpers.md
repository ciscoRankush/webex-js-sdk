# Contract - Task Handoff Summary Helper APIs

## Metadata

| Field | Value |
|---|---|
| Feature | CAI-7974 |
| Interface | Public task helper APIs |
| Producer | `Task` / `Voice` task implementation |
| Consumer | SDK consumer widget |
| Change type | new public SDK methods/types |
| Status | draft - exact method names/action vocabulary pending owner confirmation |

## Definition

The SDK exposes additive task helper APIs so consumers do not construct AI Assistant backend event payloads directly.

Proposed helper surface, final naming to be confirmed during implementation:

| Helper | Purpose | Gate |
|---|---|---|
| `requestHandoffSummary(...)` | Ask AI Assistant to generate a mid-call handoff summary for the current task. | Requires summary enablement to be true. |
| `respondToHandoffSummary(...)` | Send response action for cancel, consult, or transfer. | Requires matching task context and accepted action. |

Proposed request payload fields:

| Field | Required | Meaning |
|---|---|---|
| `interactionId` | optional if derived from task | Task interaction id. |
| `conversationId` | optional | Backend conversation id if distinct from interaction id. |
| `action` | response only | Cancel, consult, or transfer action. Exact enum to be confirmed. |
| `metadata` | optional | Backend-approved metadata only; no arbitrary summary body logging. |

## Error / Failure Catalog

| Failure | SDK behavior |
|---|---|
| Feature disabled | Return documented disabled/no-op result or reject with typed disabled error. |
| Missing task correlation id | Reject with typed validation error. |
| Unsupported action | Reject before AI Assistant call. |
| AI Assistant request failure | Reject with existing error wrapping; do not change task transfer/consult state. |

## Backward Compatibility

- Additive methods only.
- Existing `transfer`, `consult`, `endConsult`, and related voice task methods must keep current behavior.
- Helper failures must not implicitly fail transfer/consult unless the caller explicitly sequences them that way.

## Security / Privacy

- Helpers must not accept or log raw summary content unless backend contract requires it.
- Logs/metrics must include identifiers and status only.

## Versioning

- Public helper addition requires typings, docs, generated manifest, and release-note consideration.
