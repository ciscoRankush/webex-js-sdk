# Test Strategy - SDK Agent Handoff Summary Events and APIs

> Start here -> repo root [`AGENTS.md`](../../AGENTS.md) (agent entry) -> router [`SPEC_INDEX.md`](../../ai-docs/SPEC_INDEX.md) -> system [`ARCHITECTURE.md`](../../ai-docs/ARCHITECTURE.md). This plan proves the spec [`feature-spec.md`](spec/feature-spec.md) and design [`feature-design.md`](design/feature-design.md).

## Metadata

| Field | Value |
|---|---|
| Feature / ticket key | CAI-7974 |
| Feature Spec | `spec/feature-spec.md` |
| Feature Design | `design/feature-design.md` |
| Generated from | `test-strategy` @ SDLC template library `0.1.0-draft` |

## References

- Feature Spec: `spec/feature-spec.md`
- Feature Design: `design/feature-design.md`
- Repo architecture: `../../ai-docs/ARCHITECTURE.md`
- Relevant package commands: `yarn workspace @webex/contact-center run test:unit`, `test:style`, `generate:manifest`

## Test Config Variables

| Variable | Possible values |
|---|---|
| `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled` | `true`, `false`, missing |
| Runtime `FEATURE_ENABLEMENT` | enabled, disabled, malformed, not received |
| Task correlation id | matching task `interactionId`, matching `conversationId`, unknown id, missing id |
| AI Assistant request result | success, rejected error, base URL unavailable |
| Response action | cancel, consult, transfer, unsupported |
| Backend event payload | valid summary, malformed summary, duplicate summary, response for subsequent agent |

## Use Cases -> Tests

| # | Use case / acceptance criterion | Positive case | Negative case | Status |
|---|---|---|---|---|
| 1 | Gate summary request by `consultTransferSummariesEnabled` (R-1). | Enabled config calls `ApiAIAssistant` once. | False/missing config skips the call and leaves transfer/consult behavior unchanged. | planned |
| 2 | Honor runtime `FEATURE_ENABLEMENT` (R-2). | Enablement event updates later request eligibility. | Malformed event is ignored and prior gate state remains. | planned |
| 3 | Request summary through AI Assistant transport (R-3). | Helper sends expected event type/name/action/body. | AI Assistant rejection is sanitized and does not mutate task state. | planned |
| 4 | Emit task event for `MID_CALL_SUMMARY` (R-4). | Matching task receives exactly one public summary event. | Unknown task id or missing correlation id does not throw or emit. | planned |
| 5 | Emit/route subsequent-agent response event (R-5). | Matching task receives response event/result path. | Response for unknown task is ignored safely. | planned |
| 6 | Expose public helper APIs for request/respond (R-6). | Valid helper payloads resolve through `ApiAIAssistant`. | Unsupported action or missing id rejects before network call. | planned |
| 7 | Update typings/docs/manifest and avoid payload logging (R-7). | Public types compile and manifest includes additive surface. | Logger/metrics calls do not include summary text. | planned |

## Contract Tests

| Scenario / interface | Consumer | Producer | CI stage |
|---|---|---|---|
| AI Assistant handoff summary event request body | AI Assistant service | `ApiAIAssistant` / task helper | Jest unit in PR CI |
| Task summary delivery event payload | SDK consumer widget | `TaskManager` / task instance | Jest unit in PR CI |
| Task response helper action payload | AI Assistant service | `Task` / `Voice` helper | Jest unit in PR CI |

## Integration Tests

| Scenario | Suite | Automated | In CI |
|---|---|---|---|
| WebSocket `MID_CALL_SUMMARY` payload routes to existing task and emits public task event. | `test/unit/spec/services/task/TaskManager.ts` with mocked WebSocket manager | yes | yes |
| Runtime `FEATURE_ENABLEMENT` changes later helper behavior. | `test/unit/spec/services/task/TaskManager.ts` or helper-focused suite | yes | yes |
| Task helper calls `ApiAIAssistant` and preserves existing transfer/consult state on failure. | `test/unit/spec/services/task/Task.ts` / `Voice.ts` | yes | yes |
| Public facade/export and manifest generation reflect additive API. | `test/unit/spec/cc.ts`, manifest generation review | yes/manual diff | yes/manual review |

## Security Tests

| Service | Test type | Tracker |
|---|---|---|
| TaskManager / task helpers | Unit assertion that summary body is not passed into logger/metrics. | CAI-7974 |
| ApiAIAssistant request path | Unit assertion that Webex auth path is reused and no custom token is introduced. | CAI-7974 |
| Public helper validation | Unit negative tests for unsupported action and missing correlation id. | CAI-7974 |

## Resiliency Tests

| Failure injected | Expected behavior | Suite | In CI |
|---|---|---|---|
| AI Assistant request rejects. | Helper rejects/logs sanitized failure; task state remains unchanged. | Jest unit | yes |
| Feature gate disabled. | No network request; existing transfer/consult behavior unchanged. | Jest unit | yes |
| Summary WebSocket event references unknown task. | No throw; no event emitted. | Jest unit | yes |
| Malformed `FEATURE_ENABLEMENT`. | Ignore event; retain prior gate state. | Jest unit | yes |
| Duplicate summary payload. | Idempotent behavior if backend id exists; otherwise documented best-effort behavior. | Jest unit | yes |

## Production / Post-Deploy Tests

| Check | Signal it watches | Trigger | Rollback if |
|---|---|---|---|
| Canary tenant summary request | Request success/failure metric and sanitized logs. | canary | Error rate exceeds owner-defined threshold. |
| Summary event delivery | Widget receives task summary event for test consult/transfer scenario. | smoke | Summary event fails for enabled tenant. |
| Disabled tenant guardrail | No AI Assistant request when gate disabled. | synthetic/manual | Request occurs while disabled. |

## QA Dependencies

| Dependency | Needed for | Ready? |
|---|---|---|
| Backend schema for `FEATURE_ENABLEMENT` | Runtime enablement tests and contract finalization. | no - Q-1 open |
| Backend schema for `MID_CALL_SUMMARY` and response event | Payload typing and routing tests. | no - Q-1 open |
| AI Assistant accepted event names/actions | Helper request/response contract tests. | no - Q-2 open |
| PR #4794 baseline present in implementation branch | All implementation tests. | no in current checkout; yes as approved baseline assumption |

## E2E Framework & Location

- Framework: Jest through `webex-legacy-tools test --unit --runner jest`
- Test directory: `packages/@webex/contact-center/test/unit/spec`
- Tag convention: existing package unit suites, no explicit tag convention found
- Runs in CI: package `test:unit` / PR CI

## Coverage Summary

| Test type | Scenarios | Automated | In CI | Status |
|---|---|---|---|---|
| Unit | Gate, helper, routing, payload, logging, failure cases | yes | yes | planned |
| Contract | AI Assistant event body and task event/helper payload shapes | yes | yes | planned |
| Integration-style unit | WebSocket-to-task routing with mocked managers | yes | yes | planned |
| Security | Sanitized logging/metrics and auth reuse | yes | yes | planned |
| Post-deploy | Canary/smoke validation in enabled tenant | manual/synthetic | no | planned |

## Gaps / Risks

| Gap | Impact | Mitigation |
|---|---|---|
| Backend payload schemas are not confirmed in this checkout. | Type and contract docs may need adjustment during implementation. | Keep Q-1/Q-2 open and validate before coding. |
| Current branch lacks PR #4794 baseline. | Implementation tests cannot run as designed until baseline is present. | Rebase/merge PR #4794 baseline before implementation. |
| No live backend integration in unit suite. | Contract mismatch may escape unit tests. | Add contract fixtures from backend owner and canary smoke after deploy. |
