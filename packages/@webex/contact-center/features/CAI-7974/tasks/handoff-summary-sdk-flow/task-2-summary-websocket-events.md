# Task - WebSocket Summary Event Routing and Task Event Delivery

> Start here -> repo root [`AGENTS.md`](../../../../AGENTS.md) (agent entry) -> router [`SPEC_INDEX.md`](../../../../ai-docs/SPEC_INDEX.md). This task sits under [`epic.md`](./epic.md) and implements part of [`feature-design.md`](../../design/feature-design.md).

## Metadata

| Field | Value |
|---|---|
| Task title | WebSocket summary event routing and task event delivery |
| Parent epic | `./epic.md` |
| Parent feature | `../../design/feature-design.md` and `../../spec/feature-spec.md` |
| Task type | backend / API / observability |
| Target repo(s) / module | `webex/webex-js-sdk` / `@webex/contact-center` `task` |
| Tracker key | CAI-7974-T2 |
| State | not_started |
| created_by / approved_by / date | Codex generator / user-approved lifecycle write / 2026-06-24T19:02:22Z |
| Generated from | `task` @ SDLC template library `0.1.0-draft` |

## Source Mapping

- Implements: Feature Design -> WebSocket Summary Router and Task Event Publisher.
- Satisfies: R-2, R-4, R-5, part of R-7.

## Primary Code Touchpoints

- `packages/@webex/contact-center/src/services/task/TaskManager.ts` - recognize `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`; route valid messages to existing task without creating tasks for summary-only events.
- `packages/@webex/contact-center/src/services/task/constants.ts` - add summary event mapping constants if needed.
- `packages/@webex/contact-center/src/services/task/types.ts` - add task event constants and payload types for summary delivery/response sections.
- `packages/@webex/contact-center/src/services/config/types.ts` - add backend CC event names if implementation keeps backend event enum there.
- `packages/@webex/contact-center/test/unit/spec/services/task/TaskManager.ts` - add WebSocket routing tests.

## Ownership Boundary

- Owns: `src/services/task/TaskManager.ts#summary event routing`
- Owns: `src/services/task/constants.ts#summary websocket/task event constants`
- Owns: `src/services/task/types.ts#TASK_EVENTS summary event constants and summary payload event types`
- Owns: `src/services/config/types.ts#CC_TASK_EVENTS or CC_EVENTS summary names`
- Owns: `test/unit/spec/services/task/TaskManager.ts#handoff summary routing tests`
- Must NOT touch: `src/services/ApiAiAssistant.ts` -> owned by T1
- Must NOT touch: `src/services/task/Task.ts#public helper methods` -> owned by T3
- Must NOT touch: `src/services/task/types.ts#ITask helper method signatures` -> owned by T3, except for event payload types coordinated by this task

## Dependencies / Execution Stream

| This task | Depends on | Parallel-safe with | Wave / stream |
|---|---|---|---|
| T2 | PR #4794 baseline and backend payload schema confirmation | T1 for most files; coordinate shared event names/types | Wave 1 |

## Acceptance Criteria

- [ ] `FEATURE_ENABLEMENT` valid payload updates runtime summary enablement state.
- [ ] Malformed `FEATURE_ENABLEMENT` is ignored safely and does not throw.
- [ ] `MID_CALL_SUMMARY` for known task emits the public task summary event once with typed payload.
- [ ] `MID_CALL_SUMMARY` for unknown/missing task id does not throw, create a task, or emit to the wrong task.
- [ ] `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT` routes to the matching task response event/result path.
- [ ] Summary body text is not logged or used as metric attribute.

## Verifier Exit Criteria

- [ ] Independent verifier confirms TaskManager does not create tasks from summary-only events.
- [ ] Independent verifier confirms event correlation uses the backend-approved id field(s).
- [ ] Focused TaskManager tests pass for positive and negative summary event paths.
- [ ] Existing TaskManager event routing tests still pass.

## Traceability

| Requirement / rule id | Code symbol | Test that proves it |
|---|---|---|
| R-2 | `TaskManager` `FEATURE_ENABLEMENT` branch | runtime enablement update/malformed tests |
| R-4 | `TaskManager` `MID_CALL_SUMMARY` branch | known/unknown task routing tests |
| R-5 | `TaskManager` response event branch | subsequent-agent response routing tests |
| R-7 | logger/metrics calls in routing path | sanitized logging tests |

## Coverage Expectation

- Changed-line coverage >= 80%; evidence: Jest coverage output or CI coverage report for touched files.

## Cross-Cutting Prompts

- **Logging:** event type/id/status only; no raw summary content.
- **Metrics:** delivery success/ignored/failure counts if metrics taxonomy accepts them.
- **Security:** summary event body is sensitive; do not broadcast beyond matching task.
- **Idempotency:** de-duplicate by backend message id if supplied; otherwise document best effort.
- **Rollout:** safe to ship before backend emits events; branches remain dormant.

## Non-Goals / Out-of-Scope

- Does not implement AI Assistant request transport; T1 owns that.
- Does not expose task helper methods; T3 owns that.
- Does not add UI behavior.

## References

- Epic: `./epic.md`
- Feature design: `../../design/feature-design.md`
- Contract: `../../design/contracts/task-handoff-summary-events.md`
