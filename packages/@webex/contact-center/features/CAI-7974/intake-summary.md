# Intake Summary - CAI-7974

## Source

- Jira: https://jira-eng-sjc12.cisco.com/jira/browse/CAI-7974, pulled 2026-06-24T19:02:22Z through configured Jira SJC12 MCP.
- GitHub prior work: https://github.com/webex/webex-js-sdk/pull/4794, pulled through GitHub connector. PR is merged; user approved treating it as baseline for this lifecycle run.
- Repo context: `packages/@webex/contact-center/AGENTS.md`, `.sdd/manifest.json`, `ai-docs/SPEC_INDEX.md`, and `ai-docs/ARCHITECTURE.md`.

## WHAT (User Language)

Add SDK support for Agent Handoff Summary events and public task APIs so widgets can request, receive, and respond to AI-generated mid-call summaries during consult/transfer flows.

## WHY (Problem / Goal)

Transfer and consult workflows need a structured handoff summary for the subsequent agent. The SDK should centralize feature gating, AI Assistant transport, WebSocket summary handling, and public task event/API exposure so consuming widgets do not call backend AI APIs directly or duplicate task-state logic.

## Scope

**In scope:**
- Reuse the PR #4794 `ApiAIAssistant` baseline instead of recreating AI Assistant transport.
- Gate summary request behavior with `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled`.
- Handle backend `FEATURE_ENABLEMENT` if it is sent at runtime for handoff summary enablement.
- Handle `MID_CALL_SUMMARY` WebSocket messages and emit task-level summary delivery events.
- Handle `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` WebSocket messages and expose the result to the task/widget layer.
- Expose public task helper APIs for requesting handoff summary and responding with cancel, consult, or transfer actions.
- Add public typings, unit tests, SDK manifest/doc updates, and feature SDD artifacts.

**Out of scope:**
- Reimplementing PR #4794 transcript infrastructure, `ApiAIAssistant` baseline, `/event` transport, or generic AI Assistant event names.
- Building widget UI, transfer/consult popups, or Figma-driven UI flows in this SDK package.
- Backend AI Assistant service changes, WCC event producer changes, or new persistent storage.
- Changing existing transfer/consult semantics when summary feature gates are off or unsupported.
- Code implementation in this lifecycle run.

## Change Class

`contract-affecting + security`

Rationale:
- Contract-affecting: public task events/helpers/types and AI Assistant event payload contracts change.
- Security: handoff summaries may contain sensitive conversation content; logs/metrics must avoid leaking payload data and existing Webex auth/feature gates must remain authoritative.

## Touched Modules

| Module | Coverage | Why touched | Impact |
|---|---|---|---|
| `contact-center-plugin` | Partial | Public package facade and exports are affected. | Expose additive API/types, wire `ApiAIAssistant` into facade/task manager baseline, update `sdk-manifest.yaml`. |
| `config` | Partial | Summary feature gating depends on `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled` and optional runtime enablement. | Preserve PR #4794 AI feature parsing and extend/consume summary enablement state. |
| `task` | Partial | WebSocket task event routing and task event emission are the main delivery path. | Consume summary WebSocket messages, update task data/event emission, expose typed task events. |
| `voice-webrtc` | Partial | Consult/transfer flows are voice-specific and trigger request/response helpers. | Add/route helper behavior without changing existing consult/transfer outcomes when gated off. |
| `metrics` | Partial | Summary request/delivery/failure paths need serviceability. | Add/extend operational metrics without logging summary content. |

## Acceptance Signals

- The SDK checks `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled` before requesting a handoff summary.
- If backend sends `FEATURE_ENABLEMENT`, the SDK updates summary enablement without requiring a new register cycle.
- On transfer/consult summary request, the SDK calls the existing AI Assistant `/event` path with the correct event name/action and existing Webex auth.
- `MID_CALL_SUMMARY` WebSocket messages are routed to the correct task and emitted as a public task event with typed payload.
- `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` WebSocket messages are routed to the correct task/widget-facing event path.
- Public task helpers exist for request and response paths, including cancel, consult, and transfer actions.
- Existing transfer/consult behavior remains unchanged when gates are disabled, payloads are malformed, backend events are missing, or AI Assistant requests fail.
- Unit tests cover positive and negative cases for feature gating, request payloads, WebSocket routing, event emission, helper methods, and sensitive logging boundaries.
- Public typings/docs and `sdk-manifest.yaml` are updated for the additive SDK surface.

## Open Questions

- **Q-1 Exact backend payload schema for `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`** - owner: backend/API owner - status: open - deferred to implementation contract validation.
- **Q-2 Exact response action vocabulary for cancel, consult, and transfer** - owner: product/backend owner - status: open - deferred to implementation contract validation.
- **Q-3 Gate strictness for `consultTransferSummariesEnabled`** - owner: product/backend owner - status: open - default design uses fail-closed unless owner confirms permissive behavior.

## Contracts Delta (Preliminary)

**Provides:**
- ADDED public task event for mid-call summary delivery.
- ADDED public task event or state callback for subsequent-agent summary response delivery.
- ADDED public task helper(s) for requesting and responding to mid-call handoff summary.
- MODIFIED public package typings and SDK manifest.

**Requires:**
- MODIFIED use of existing AI Assistant `/event` transport from PR #4794 with handoff summary event names/actions.
- ADDED consumption of WCC WebSocket event types `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`.
- REQUIRES `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled` from PR #4794 baseline.

## Stakeholders

| Role | Interest | Sign-off needed? |
|---|---|---|
| Contact Center SDK owner | Public API shape, task event behavior, tests, package manifest. | yes |
| AI Assistant/backend owner | Event names, payload schema, enablement semantics, `/event` response contract. | yes |
| Widget/desktop consumer owner | Consumes task summary events and response helpers. | yes |
| Security/privacy reviewer | Summary payload sensitivity and logging/metric boundaries. | yes |
| QA owner | Unit/integration coverage for feature gates, event routing, and negative paths. | yes |

## Provenance

- Created by: Codex generator runtime.
- Created at: 2026-06-24T19:02:22Z.
- Source: Jira CAI-7974, GitHub PR #4794, package-local SDD manifest/docs.
- Approved by: User approval in Codex chat for feature file creation and PR #4794 baseline assumption.
