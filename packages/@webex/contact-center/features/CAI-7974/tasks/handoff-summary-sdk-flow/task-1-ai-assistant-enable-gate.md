# Task - AI Assistant Handoff Transport and Enablement Gate

> Start here -> repo root [`AGENTS.md`](../../../../AGENTS.md) (agent entry) -> router [`SPEC_INDEX.md`](../../../../ai-docs/SPEC_INDEX.md). This task sits under [`epic.md`](./epic.md) and implements part of [`feature-design.md`](../../design/feature-design.md).

## Metadata

| Field | Value |
|---|---|
| Task title | AI Assistant handoff transport and enablement gate |
| Parent epic | `./epic.md` |
| Parent feature | `../../design/feature-design.md` and `../../spec/feature-spec.md` |
| Task type | API / validation / observability |
| Target repo(s) / module | `webex/webex-js-sdk` / `@webex/contact-center` `config`, `contact-center-plugin`, `metrics` |
| Tracker key | CAI-7974-T1 |
| State | not_started |
| created_by / approved_by / date | Codex generator / user-approved lifecycle write / 2026-06-24T19:02:22Z |
| Generated from | `task` @ SDLC template library `0.1.0-draft` |

## Source Mapping

- Implements: Feature Design -> Feature Gate Resolver and Handoff Summary Request Adapter.
- Satisfies: R-1, R-3, part of R-7.

## Primary Code Touchpoints

- `packages/@webex/contact-center/src/services/ApiAiAssistant.ts` - extend PR #4794 service with handoff summary request/response event methods or safe generic helper usage.
- `packages/@webex/contact-center/src/services/constants.ts` - add/confirm AI Assistant handoff summary event URL/name constants if not already represented in `types.ts`.
- `packages/@webex/contact-center/src/types.ts` - add AI Assistant handoff summary event name/action types when implementation chooses public/shared placement.
- `packages/@webex/contact-center/src/services/config/types.ts` - confirm `AIFeatureFlags.generatedSummaries.consultTransferSummariesEnabled` shape from PR #4794.
- `packages/@webex/contact-center/src/services/config/Util.ts` - preserve AI feature parsing from PR #4794 baseline.
- `packages/@webex/contact-center/src/metrics/constants.ts` and metric helpers - add sanitized success/failure/disabled metric names if needed.
- `packages/@webex/contact-center/test/unit/spec/services/ApiAiAssistant.ts` - add request/response helper and error tests.
- `packages/@webex/contact-center/test/unit/spec/services/config/index.ts` - add/adjust AI feature parsing gate tests if baseline lacks coverage.

## Ownership Boundary

- Owns: `src/services/ApiAiAssistant.ts`
- Owns: `src/services/constants.ts#AI_ASSISTANT_*`
- Owns: `src/services/config/types.ts#AIFeatureFlags`
- Owns: `src/services/config/Util.ts#aiFeature parsing`
- Owns: `src/metrics/constants.ts#AI_ASSISTANT_*` metric additions
- Owns: `test/unit/spec/services/ApiAiAssistant.ts`
- Must NOT touch: `src/services/task/TaskManager.ts` -> owned by T2
- Must NOT touch: `src/services/task/Task.ts`, `src/services/task/voice/Voice.ts`, public helper method implementation -> owned by T3

## Dependencies / Execution Stream

| This task | Depends on | Parallel-safe with | Wave / stream |
|---|---|---|---|
| T1 | PR #4794 baseline and backend event-name/action confirmation for AI Assistant request path | Mostly T2; coordinate shared event names/types | Wave 1 |

## Acceptance Criteria

- [ ] Summary request helper checks register-time summary gate before calling AI Assistant.
- [ ] Disabled/missing `consultTransferSummariesEnabled` path makes no AI Assistant request.
- [ ] Enabled path sends backend-approved handoff summary event type/name/action/body through existing `/event` transport.
- [ ] AI Assistant request failure is logged/metriced without summary body text.
- [ ] Unit tests cover enabled, disabled, missing flag, request rejection, and sanitized logging.

## Verifier Exit Criteria

- [ ] Independent verifier confirms no new credentials/tokens are introduced.
- [ ] Independent verifier confirms summary payload/body is not logged or sent as a metric attribute.
- [ ] `yarn workspace @webex/contact-center run test:unit --runTestsByPath test/unit/spec/services/ApiAiAssistant.ts` or package-equivalent focused test passes.
- [ ] Relevant config parsing tests pass if touched.

## Traceability

| Requirement / rule id | Code symbol | Test that proves it |
|---|---|---|
| R-1 | `ApiAIAssistant` summary helper / gate check | `test/unit/spec/services/ApiAiAssistant.ts` disabled and enabled cases |
| R-3 | `ApiAIAssistant.sendEvent` or handoff-specific wrapper | request body success/failure tests |
| R-7 | metrics/logger calls | sanitized logging negative test |

## Coverage Expectation

- Changed-line coverage >= 80%; evidence: Jest coverage output or CI coverage report for touched files.

## Cross-Cutting Prompts

- **Logging:** log event name/status/interaction id only; no summary body.
- **Metrics:** add request success/failure/disabled metrics if not already covered by existing AI Assistant metrics.
- **Security:** reuse Webex auth, fail closed on disabled/missing gate.
- **Idempotency:** helper should not retry automatically unless existing request infrastructure does so.
- **Rollout:** safe to ship with gate defaulting off/fail-closed.

## Non-Goals / Out-of-Scope

- Does not route WebSocket summary events; T2 owns that.
- Does not add public task helper methods; T3 owns that.
- Does not implement backend service changes.

## Feature-Flag & Rollout Assumptions

- Flag: `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled`
- Default: fail closed when missing/false
- Assumption: backend controls enablement and PR #4794 parsing baseline exists.

## References

- Epic: `./epic.md`
- Feature design: `../../design/feature-design.md`
- Contract: `../../design/contracts/ai-assistant-handoff-summary-event.md`
