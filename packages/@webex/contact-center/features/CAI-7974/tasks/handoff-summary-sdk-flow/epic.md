# Epic - Handoff Summary SDK Flow

> Start here -> repo root [`AGENTS.md`](../../../../AGENTS.md) (agent entry) -> router [`SPEC_INDEX.md`](../../../../ai-docs/SPEC_INDEX.md). This epic delivers a slice of the design [`feature-design.md`](../../design/feature-design.md); its child tasks map to implementation-ready units.

## Metadata

| Field | Value |
|---|---|
| Epic title | Handoff Summary SDK Flow |
| Parent feature | `../../design/feature-design.md` and `../../spec/feature-spec.md` |
| Service group / capability | SDK-owned handoff summary request, delivery, response, and public API surface |
| Tracker / Epic key | CAI-7974 |
| Status | ready |
| created_by / approved_by / date | Codex generator / user-approved lifecycle write / 2026-06-24T19:02:22Z |
| Generated from | `epic` @ SDLC template library `0.1.0-draft` |

## Scope - the slice of the design this epic delivers

This epic delivers the Contact Center SDK side of Agent Handoff Summary: feature enablement, AI Assistant request/response helpers, WebSocket summary event routing, task-scoped public event emission, typings, docs, manifest, and tests. Backend event production, AI Assistant service changes, widget UI, and persistent storage are out of scope.

## Mapped Design Sections

| Feature design section | What this epic delivers from it |
|---|---|
| Functional-Block Decomposition | Implements Feature Gate Resolver, Handoff Summary Request Adapter, WebSocket Summary Router, Task Event Publisher, and Public Task Helper Facade. |
| Interface & Contract Definitions | Implements contract docs for AI Assistant `/event`, task summary events, and task helper APIs. |
| Security / RBAC Design | Preserves Webex auth, feature gating, and sensitive payload logging restrictions. |
| Rollout / Migration Interlock | Ships additive code fail-closed and depends on PR #4794 baseline. |
| Test Strategy | Adds Jest unit/contract-style tests and manifest/docs checks. |

## Summary of Changes (per service / module)

- **`contact-center-plugin`:** additive public exports/types/manifest updates for handoff summary helpers/events.
- **`config`:** consume existing AI feature flags and optional runtime enablement for consult transfer summaries.
- **`task`:** route backend summary events and emit typed task-scoped SDK events.
- **`voice-webrtc`:** expose summary helper behavior around consult/transfer flows without changing existing operations.
- **`metrics`:** add sanitized operational metrics for request/delivery/failure/disabled paths.

## Baseline vs This Epic

| Capability | Already shipped / approved baseline | Added by this epic |
|---|---|---|
| AI Assistant service transport | PR #4794 `ApiAIAssistant`, `/event`, AI feature flag parsing, generic event names. | Handoff summary event names/actions and helper usage. |
| Task WebSocket routing | Existing `TaskManager` maps task events and emits state-machine/task events. | Summary-specific event routing and runtime enablement handling. |
| Task public operations | Existing transfer/consult/end/voice operations. | Additive summary request/respond helper APIs and task events. |
| Tests/docs/manifest | Existing package unit suites and SDD docs. | Feature-specific tests, docs, contracts, and manifest updates. |

## Child Tasks

| Task | One-line | PR-sized? | Task doc | Tracker key |
|---|---|---|---|---|
| T1 | AI Assistant handoff transport and summary enablement gate. | yes | `task-1-ai-assistant-enable-gate.md` | CAI-7974-T1 |
| T2 | WebSocket summary event routing and task event delivery. | yes | `task-2-summary-websocket-events.md` | CAI-7974-T2 |
| T3 | Public task helpers, typings, docs, manifest, and final tests. | yes | `task-3-public-task-helpers.md` | CAI-7974-T3 |

## Sequencing & Dependencies

| Task / epic | Depends on | Parallel-safe with | Wave |
|---|---|---|---|
| T1 AI Assistant enable/gate | PR #4794 baseline | T2 after shared constants are agreed | 1 |
| T2 WebSocket summary events | PR #4794 baseline; backend payload schema confirmation | T1 for most files, but coordinate event constants/types | 1 |
| T3 Public task helpers | T1 helper transport and T2 event payload types | none for shared type sections; can start docs/tests after contracts stabilize | 2 |

## Rollout Order

1. Ensure PR #4794 baseline is present in the implementation branch.
2. Land T1 and T2 fail-closed with unit tests.
3. Land T3 helper/docs/manifest updates after T1/T2 contracts are stable.
4. Enable in integration/canary tenants through backend feature flags.

## Exit Criteria

- [ ] Feature gates fail closed and disabled path has no AI Assistant call.
- [ ] Summary request and response helper payloads match backend-approved contract.
- [ ] `MID_CALL_SUMMARY` and response events route only to the matching task.
- [ ] Public task helpers/events/types are documented and reflected in generated manifest.
- [ ] Unit tests cover positive and negative paths for all requirements.
- [ ] Summary content is not logged or sent as metric attributes.

## References

- Feature design: `../../design/feature-design.md`
- Feature spec: `../../spec/feature-spec.md`
- Test strategy: `../../test-strategy.md`
- Child tasks: `./task-*.md`
