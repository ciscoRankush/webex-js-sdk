# Contract - AI Assistant Handoff Summary Event

## Metadata

| Field | Value |
|---|---|
| Feature | CAI-7974 |
| Interface | AI Assistant `/event` request for handoff summary |
| Producer | `@webex/contact-center` SDK via `ApiAIAssistant` |
| Consumer | AI Assistant service |
| Change type | modify existing PR #4794 transport |
| Status | draft - backend schema confirmation pending |

## Definition

The SDK reuses the PR #4794 `ApiAIAssistant` service and its authenticated POST `/event` transport. CAI-7974 adds handoff-summary-specific event names/actions on top of that transport.

Expected request fields, subject to backend owner confirmation:

| Field | Required | Meaning |
|---|---|---|
| `agentId` | yes | Current agent identifier from SDK runtime config. |
| `orgId` | yes | Webex org id from credentials. |
| `eventType` | yes | AI Assistant event type, expected `CUSTOM_EVENT` unless backend specifies otherwise. |
| `eventName` | yes | `GET_MID_CALL_SUMMARY` for request; `MID_CALL_SUMMARY_RESPONSE` for response path, unless backend confirms different names. |
| `eventDetails.data.interactionId` or equivalent | yes | Task/interaction correlation id. |
| `eventDetails.data.action` | conditional | Response action for cancel, consult, or transfer. |
| `eventDetails.data.actionTimeStamp` | yes | Client-side event timestamp, following PR #4794 pattern. |

## Error / Failure Catalog

| Failure | SDK behavior |
|---|---|
| Feature gate disabled/missing | Do not call `/event`; return documented disabled/no-op behavior. |
| AI Assistant base URL unavailable | Throw/reject using existing `ApiAIAssistant` error wrapping. |
| `/event` request rejects | Log/metric sanitized failure and preserve existing task state. |
| Backend schema owner rejects action/name | Treat as implementation blocker until Q-1/Q-2 are resolved. |

## Backward Compatibility

- Additive to PR #4794 transport.
- Must not change real-time transcript behavior or existing AI Assistant event names.
- Existing transfer/consult methods must not depend on summary request success.

## Security / Privacy

- Use existing Webex auth; no new credentials.
- Do not log summary text or full request body.
- Allowed logs: method, event name, interaction id, status, sanitized error.

## Versioning

- Additive SDK minor surface, pending package release policy.
- Backend event names/actions must be treated as external contract inputs.
