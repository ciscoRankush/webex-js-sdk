# Feature Spec - SDK Agent Handoff Summary Events and APIs

> Start here -> repo root [`AGENTS.md`](../../../AGENTS.md) (agent entry) -> router [`SPEC_INDEX.md`](../../../ai-docs/SPEC_INDEX.md) -> system [`ARCHITECTURE.md`](../../../ai-docs/ARCHITECTURE.md). Then this spec -> design [`feature-design.md`](../design/feature-design.md) -> test plan [`test-strategy.md`](../test-strategy.md).

## Metadata

| Field | Value |
|---|---|
| Feature / ticket key | CAI-7974 |
| Title | SDK Agent Handoff Summary events and public APIs |
| Status | groomed |
| Change class | `contract-affecting + security` |
| created_by / approved_by / date | Codex generator / user-approved lifecycle write / 2026-06-24T19:02:22Z |
| Generated from | `feature-spec` @ SDLC template library `0.1.0-draft` |

## Problem & Goal (WHAT + WHY)

**What:** Add SDK support for Agent Handoff Summary events and public task APIs so widgets can request, receive, and respond to AI-generated mid-call summaries during consult/transfer flows.

**Why:** Transfer and consult workflows need a structured handoff summary for the subsequent agent. Centralizing this in the SDK preserves task-state ownership, feature gating, auth, event routing, and sensitive-data handling instead of pushing those responsibilities into each consuming widget.

## Stakeholders & Open Questions

| Stakeholder / role | Interest in this feature | Sign-off needed? |
|---|---|---|
| Contact Center SDK owner | Public SDK surface, task event behavior, package docs, manifest. | yes |
| AI Assistant/backend owner | Event names, payload schemas, enablement behavior, `/event` contract. | yes |
| Widget/desktop consumer owner | Receives handoff summaries and invokes response helpers. | yes |
| Security/privacy reviewer | Summary payload sensitivity, auth, logging, and metrics boundaries. | yes |
| QA owner | Positive and negative test coverage across gates, events, and helpers. | yes |

**Open questions:**
- **Q-1 Exact backend payload schema for `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`** - owner: backend/API owner - blocks: final contract implementation/tests - status: open, deferred to implementation contract validation.
- **Q-2 Exact response action vocabulary for cancel, consult, and transfer** - owner: product/backend owner - blocks: helper payload validation - status: open, deferred to implementation contract validation.
- **Q-3 Gate strictness for `consultTransferSummariesEnabled`** - owner: product/backend owner - blocks: gate behavior - status: open; design defaults to fail-closed.

## Scope

**In scope:**
- Reuse the PR #4794 `ApiAIAssistant` baseline and AI Assistant `/event` transport.
- Gate summary requests with `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled`.
- Handle `FEATURE_ENABLEMENT` if backend sends summary enablement at runtime.
- Handle `MID_CALL_SUMMARY` and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` WebSocket messages.
- Emit public task events for handoff summary delivery and subsequent-agent response delivery.
- Expose task helper APIs for requesting a mid-call handoff summary and responding with cancel, consult, or transfer actions.
- Add public typings, unit tests, docs, and `sdk-manifest.yaml` updates.

**Out of scope:**
- Rebuilding PR #4794 transcript infrastructure, `ApiAIAssistant`, generic AI Assistant event names, or baseline feature-flag parsing.
- Building widget UI, transfer/consult popup UI, or Figma-tracked screen changes.
- Backend AI Assistant service changes, WCC event producer changes, or persistent storage.
- Changing existing transfer/consult task behavior when summary feature gates are off or unsupported.
- Code implementation in this SDLC lifecycle run.

**Open decisions:** Q-1 through Q-3 remain owner-confirmed implementation details; they do not block the feature spec because the SDK behavior can be specified fail-closed with typed placeholders.

## Requirements

| Req ID | Requirement (WHAT) | Rationale (WHY) | Acceptance (how proven) | State |
|---|---|---|---|---|
| R-1 | The SDK must not request a handoff summary unless `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled` allows the flow. | Prevents unsupported AI summary calls and preserves tenant/profile entitlement behavior. | Unit tests prove disabled/missing flag paths do not call AI Assistant and do not alter transfer/consult behavior. | Agreed |
| R-2 | The SDK must update summary enablement if backend sends a `FEATURE_ENABLEMENT` WebSocket event. | Backend may change enablement after registration; SDK should honor current backend state. | TaskManager/facade tests prove runtime enablement changes affect later summary requests. | Draft |
| R-3 | The SDK must request a mid-call handoff summary through the existing `ApiAIAssistant` `/event` transport when a transfer or consult summary request is triggered. | Reuses the shipped AI Assistant service and centralizes Webex auth, base URL resolution, metrics, and error handling. | Tests assert event type/name/action/body fields and that request failures are logged/metriced without breaking task operations. | Agreed |
| R-4 | The SDK must route `MID_CALL_SUMMARY` WebSocket messages to the matching task and emit a public task event with a typed summary payload. | Widgets need a task-scoped event to display or consume the summary. | Tests emit WebSocket payloads and assert the correct task receives the public event once with the expected payload. | Agreed |
| R-5 | The SDK must route `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` messages to the matching task/widget-facing event path. | The subsequent agent response must be observable by SDK consumers. | Tests prove response messages route to the correct task and malformed/unmatched messages are ignored safely. | Draft |
| R-6 | The SDK must expose task helper APIs for request and response paths, including cancel, consult, and transfer actions. | Consuming widgets need a stable SDK method rather than constructing backend event payloads. | Type tests/unit tests prove helper payload validation, AI Assistant invocation, disabled behavior, and error propagation. | Draft |
| R-7 | Public typings, docs, generated SDK manifest, and unit tests must be updated with no sensitive summary content logged. | Package consumers rely on typed public surface; summaries may include sensitive conversation data. | `test:unit`, `test:style`, manifest generation diff review, and log assertions pass. | Agreed |

## Acceptance Criteria

- Summary request is skipped and existing task transfer/consult flows continue unchanged when summary enablement is missing or false (R-1).
- A valid runtime `FEATURE_ENABLEMENT` message changes later summary-request gating without requiring a new `register()` call (R-2).
- Request helpers call `ApiAIAssistant` with the handoff summary event name/action and the correct agent/task identifiers (R-3, R-6).
- `MID_CALL_SUMMARY` WebSocket payloads emit a typed public task event only for the matching task (R-4).
- `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` payloads emit/resolve the documented response path only for the matching task (R-5).
- Malformed, unmatched, disabled, or failed AI Assistant paths are logged/metriced without leaking summary text and without corrupting task state (R-1, R-3, R-7).
- Additive public API/types/events appear in docs and `sdk-manifest.yaml` (R-6, R-7).

## Success & Guardrail Metrics

| Metric | Type | Baseline | Target / bound | How measured |
|---|---|---|---|---|
| Handoff summary request attempted when enabled | success | No existing handoff summary request path. | Request helper calls AI Assistant exactly once per explicit trigger. | Unit tests and operational metric event. |
| Handoff summary delivery event emitted | success | No public event. | `MID_CALL_SUMMARY` produces one task event for matching task. | TaskManager unit test and event metric. |
| Existing transfer/consult behavior when disabled | guardrail | Existing task transfer/consult tests pass. | Disabled summary gates do not call AI Assistant or change transfer/consult results. | Existing and new unit tests. |
| Sensitive payload logging | guardrail | Existing no-token/no-authorization logging rule. | Summary text/payload body is not written to logs/metrics. | Unit assertions and code review. |
| Public API manifest currency | guardrail | Existing `sdk-manifest.yaml` generated from package surface. | Manifest reflects additive helpers/events/types when implementation lands. | `yarn workspace @webex/contact-center run generate:manifest`. |

## Prior-Work Register

| Existing artifact | How it relates | Reuse / extend / supersede |
|---|---|---|
| GitHub PR #4794 `feat(contact-center): real time transcript` | Baseline adds `ApiAIAssistant`, AI feature flags, generic AI Assistant event names, and transcript request patterns. | extend |
| `src/services/task/TaskManager.ts` | Current WebSocket event pipeline parses task messages, finds tasks by interaction id, updates task data, and emits state-machine events. | extend |
| `src/services/task/Task.ts` and `src/services/task/voice/Voice.ts` | Current public/common task operations and voice consult/transfer behavior. | extend |
| `src/services/config/Util.ts` and `src/services/config/types.ts` | Aggregate register-time config shape and parsed profile object. PR #4794 extends this with AI feature flags. | extend |
| `src/services/task/ai-docs/task-spec.md` | Documents task event pipeline, task data updates, and TaskManager responsibilities. | extend |
| `src/ai-docs/contact-center-plugin-spec.md` | Documents public package surface and manifest obligations. | extend |

## Contracts Delta

**Provides:**
- ADDED public task event for mid-call handoff summary delivery. Full contract: `../design/contracts/task-handoff-summary-events.md`.
- ADDED public task response event or result path for subsequent-agent handoff summary response. Full contract: `../design/contracts/task-handoff-summary-events.md`.
- ADDED public task helper API(s) for requesting and responding to handoff summaries. Full contract: `../design/contracts/task-handoff-summary-helpers.md`.
- MODIFIED public types and generated SDK manifest to include additive API/event contracts.

**Requires:**
- MODIFIED use of PR #4794 `ApiAIAssistant` `/event` transport with handoff summary event names/actions. Full contract: `../design/contracts/ai-assistant-handoff-summary-event.md`.
- ADDED consumption of backend WebSocket event types `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`.
- REQUIRES AI feature flag `generatedSummaries.consultTransferSummariesEnabled` from register-time or runtime enablement source.

## Impacted Modules / Repos

| Module / repo | Impact | Manifest coverage state |
|---|---|---|
| `contact-center-plugin` | Public exports/types/manifest and facade wiring for AI Assistant baseline. | Partial |
| `config` | AI feature flag and runtime enablement inputs. | Partial |
| `task` | WebSocket event routing, task event emission, helper interfaces/types. | Partial |
| `voice-webrtc` | Consult/transfer request trigger integration without changing existing operations. | Partial |
| `metrics` | Summary request/delivery/failure observability without payload leakage. | Partial |

## Feasibility & Risks

- **Feasibility:** Feasible as an additive SDK feature if PR #4794 is present as baseline. The current checkout is not descended from PR #4794; this lifecycle run explicitly assumes that merge as prior work by user approval.
- **Spikes needed:** Confirm backend payload schema and action vocabulary before coding T1/T2/T3.

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Current branch lacks PR #4794 baseline. | high in this checkout | high | Rebase/merge baseline before implementation, or include the baseline as a prerequisite task outside CAI-7974 remaining scope. |
| Backend event schema differs from assumed placeholder shape. | medium | high | Keep contract questions open and validate with backend owner before implementation. |
| Summary payload leaks in logs/metrics. | medium | high | Log identifiers/status only; add negative tests for summary text in logs. |
| Task helper overlaps existing transfer/consult semantics. | medium | medium | Gate helper behavior separately; do not mutate transfer/consult result path when summary is disabled or fails. |

## Interaction / Scenario Matrix

| Scenario / condition combination | Expected behavior | Covered by |
|---|---|---|
| Feature flag disabled or missing x request helper called | No AI Assistant request; helper returns disabled/no-op response or documented error; transfer/consult is unchanged. | R-1 / T1, T3 tests |
| Feature flag enabled x request helper called | SDK sends handoff summary request through `ApiAIAssistant`. | R-3, R-6 / T1, T3 tests |
| `FEATURE_ENABLEMENT` disables summaries after register | Later request helper does not call AI Assistant. | R-2 / T2 tests |
| `MID_CALL_SUMMARY` for known task | Matching task emits public summary event with typed payload. | R-4 / T2 tests |
| `MID_CALL_SUMMARY` for unknown task | Message is ignored/logged without throwing or creating a task. | R-4, R-7 / T2 tests |
| AI Assistant request fails | Failure is metriced/logged without summary payload; existing task state is preserved. | R-3, R-7 / T1/T3 tests |
| Subsequent-agent response event arrives | Matching task exposes response through documented event/result path. | R-5 / T2 tests |

## Product-Requested Data / Fields

| Field | Meaning / requirement |
|---|---|
| `generatedSummaries.consultTransferSummariesEnabled` | Register-time gate for consult transfer summary flow. |
| `interactionId` / `conversationId` | Correlates request/summary/response to the task. Exact backend field name to be confirmed. |
| `summary` / summary payload | AI-generated handoff content delivered to widget. Exact field shape to be confirmed. |
| `action` | Response helper action: cancel, consult, transfer. Exact backend vocabulary to be confirmed. |
| `agentId` / `orgId` | Existing AI Assistant transport identifiers. |

## Migration Expectations

- Additive SDK behavior must be gated and backward compatible.
- Existing clients that do not call the new helpers or listen for new events must see no behavior change.
- Feature gates default to fail-closed when missing or explicitly disabled.
- Public API additions imply a minor additive surface update, not a breaking change.

## Compliance & Security

- Summary payloads may include sensitive customer conversation data and must not be logged wholesale.
- Existing Webex auth (`addAuthHeader: true` in PR #4794 AI Assistant transport) remains the only SDK-side auth mechanism.
- Feature enablement must be honored before any summary request.
- Security/privacy reviewer should approve payload logging, metric attributes, and docs before implementation-ready.

## Rollout & Flags

| Flag | Purpose | Default | New/Existing |
|---|---|---|---|
| `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled` | Gates consult/transfer handoff summary requests. | Fail closed when missing/false. | Existing from PR #4794 baseline |
| Runtime `FEATURE_ENABLEMENT` state | Allows backend to update handoff summary enablement after register. | No override until event received. | New consumption |

## Serviceability

- Add operational metrics for request success/failure, summary delivery, response delivery, and disabled/skipped request decisions.
- Logs should include module/method, interaction id, event type, and status but not summary body text.
- Error paths must preserve existing task state and include enough tracking identifiers for support.

## Documentation Obligations

- Update public typings and generated `sdk-manifest.yaml`.
- Update API docs or README snippets for task summary helpers/events.
- Update package SDD docs or contracts if public surface/event semantics change.
- Add feature docs in this package under `features/CAI-7974/`.

## API / Event Contract

- AI Assistant request/response contract: `../design/contracts/ai-assistant-handoff-summary-event.md`.
- Task event/helper contracts: `../design/contracts/task-handoff-summary-events.md` and `../design/contracts/task-handoff-summary-helpers.md`.
- API registry/dev-portal review needed: no external developer portal route is added by this package, but SDK public API review is needed.

## Event Contract

- Consumes backend WebSocket events: `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`.
- Publishes SDK task events for mid-call summary delivery and subsequent-agent response delivery.
- Delivery is task-scoped; messages without a matching task are ignored safely.
- Ordering guarantee is best-effort based on WebSocket order; no persistent replay is introduced by this SDK feature.

## Public API & Semver Impact

- Adds task helper APIs for request/respond handoff summary paths.
- Adds public event constants/types for summary delivery and response delivery.
- Adds/extends payload types for summary request, summary response, and feature enablement.
- Semver impact: additive minor surface change, assuming no existing API signature changes.

## Spec State

| Section | State |
|---|---|
| Problem & Goal | complete |
| Stakeholders & Open Questions | partial |
| Scope | complete |
| Requirements | complete |
| Acceptance Criteria | complete |
| Success & Guardrail Metrics | complete |
| Prior-Work Register | complete |
| Contracts Delta | complete |
| Impacted Modules | complete |
| Conditional sections | partial - backend payload details remain open |

## Change Log

| Date | Change | By | Why |
|---|---|---|---|
| 2026-06-24 | Initial frozen feature spec from Jira CAI-7974, PR #4794, and package SDD context. | Codex | Run SDLC lifecycle through decomposition before implementation. |

## References

- Jira: https://jira-eng-sjc12.cisco.com/jira/browse/CAI-7974
- Prior baseline: https://github.com/webex/webex-js-sdk/pull/4794
- Feature design: `../design/feature-design.md`
- Decomposition: `../tasks/`
- Repo architecture: `../../../ai-docs/ARCHITECTURE.md`
- Test plan: `../test-strategy.md`
- Intake: `../questionnaire.md`
