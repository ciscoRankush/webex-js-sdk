# Task - Public Task Helpers, Typings, Docs, and Manifest

> Start here -> repo root [`AGENTS.md`](../../../../AGENTS.md) (agent entry) -> router [`SPEC_INDEX.md`](../../../../ai-docs/SPEC_INDEX.md). This task sits under [`epic.md`](./epic.md) and implements part of [`feature-design.md`](../../design/feature-design.md).

## Metadata

| Field | Value |
|---|---|
| Task title | Public task helpers, typings, docs, and manifest |
| Parent epic | `./epic.md` |
| Parent feature | `../../design/feature-design.md` and `../../spec/feature-spec.md` |
| Task type | API / docs / validation |
| Target repo(s) / module | `webex/webex-js-sdk` / `@webex/contact-center` `task`, `voice-webrtc`, `contact-center-plugin` |
| Tracker key | CAI-7974-T3 |
| State | not_started |
| created_by / approved_by / date | Codex generator / user-approved lifecycle write / 2026-06-24T19:02:22Z |
| Generated from | `task` @ SDLC template library `0.1.0-draft` |

## Source Mapping

- Implements: Feature Design -> Public Task Helper Facade, Public Surface/Docs, and final manifest/test integration.
- Satisfies: R-6, R-7, and integration coverage for R-1 through R-5.

## Primary Code Touchpoints

- `packages/@webex/contact-center/src/services/task/Task.ts` - add generic task helper methods if they apply to all task types.
- `packages/@webex/contact-center/src/services/task/voice/Voice.ts` - add/override voice-specific request/respond behavior for consult/transfer flows if needed.
- `packages/@webex/contact-center/src/services/task/types.ts` - add `ITask` helper signatures, payload/result types, and public event payload types not owned by T2.
- `packages/@webex/contact-center/src/index.ts` - export public types/helpers if package barrel updates are required.
- `packages/@webex/contact-center/src/types.ts` - add public/shared AI Assistant action/event types if final placement belongs at package root.
- `packages/@webex/contact-center/sdk-manifest.yaml` - regenerate after public API changes.
- `packages/@webex/contact-center/test/unit/spec/services/task/Task.ts` - add base helper tests if generic.
- `packages/@webex/contact-center/test/unit/spec/services/task/voice/Voice.ts` - add voice-specific helper tests.
- `packages/@webex/contact-center/test/unit/spec/cc.ts` - add facade/export/manifest-adjacent assertions if needed.
- Package README/API docs or SDD docs touched by public surface changes.

## Ownership Boundary

- Owns: `src/services/task/Task.ts#handoff summary helper methods`
- Owns: `src/services/task/voice/Voice.ts#voice-specific handoff helper behavior`
- Owns: `src/services/task/types.ts#ITask helper signatures and helper payload/result types`
- Owns: `src/index.ts#handoff summary public exports`
- Owns: `src/types.ts#public handoff summary shared types`, if implementation places them there
- Owns: `sdk-manifest.yaml`
- Owns: docs/readme snippets for the public helper/event surface
- Must NOT touch: `src/services/ApiAiAssistant.ts` -> owned by T1
- Must NOT touch: `src/services/task/TaskManager.ts#summary websocket routing` -> owned by T2
- Must coordinate with T2 before editing shared `src/services/task/types.ts` event sections.

## Dependencies / Execution Stream

| This task | Depends on | Parallel-safe with | Wave / stream |
|---|---|---|---|
| T3 | T1 request transport and T2 event payload types | Not parallel-safe for shared public type finalization | Wave 2 |

## Acceptance Criteria

- [ ] Public task helper method(s) exist with typed request/response payloads.
- [ ] Valid request/respond helper calls use the T1 AI Assistant transport.
- [ ] Disabled/unsupported helper paths reject or no-op according to documented contract and do not call AI Assistant.
- [ ] Unsupported response action rejects before network call.
- [ ] Existing `transfer`, `consult`, and `endConsult` behavior remains unchanged in existing tests.
- [ ] Public event/helper types are exported where package conventions require.
- [ ] `sdk-manifest.yaml` is regenerated or confirmed unchanged if helpers are not manifest-visible.
- [ ] Docs describe helper usage, event subscription, disabled behavior, and privacy/logging caveats.

## Verifier Exit Criteria

- [ ] Independent verifier confirms public APIs are additive and no existing method signature is broken.
- [ ] Independent verifier confirms manifest/docs match implemented public surface.
- [ ] Focused Task/Voice/cc tests pass.
- [ ] `yarn workspace @webex/contact-center run test:style` passes for touched source.
- [ ] No implementation plan/code work proceeds until this task document is accepted.

## Traceability

| Requirement / rule id | Code symbol | Test that proves it |
|---|---|---|
| R-6 | `Task.requestHandoffSummary` / `Task.respondToHandoffSummary` or final approved helper names | Task/Voice helper unit tests |
| R-7 | public exports/types and `sdk-manifest.yaml` | cc/export tests, manifest diff review, docs checks |
| R-1 | helper gate integration | disabled helper test |
| R-3 | helper-to-ApiAIAssistant path | helper success/failure tests |

## Coverage Expectation

- Changed-line coverage >= 80%; evidence: Jest coverage output or CI coverage report for touched files.

## Cross-Cutting Prompts

- **Logging:** helper methods must not log summary body or arbitrary metadata.
- **Metrics:** helper success/failure/disabled events should align with T1 metrics.
- **Security:** validate action enum and correlation id before network calls.
- **Idempotency:** helper should not auto-repeat response actions unless caller invokes it again.
- **Rollout:** additive API can ship fail-closed before backend rollout.

## Non-Goals / Out-of-Scope

- Does not add widget UI.
- Does not implement backend API or WebSocket event production.
- Does not modify existing transfer/consult semantics beyond optional summary helper calls.

## Feature-Flag & Rollout Assumptions

- Flag: `agentConfig.aiFeature.generatedSummaries.consultTransferSummariesEnabled`
- Default: fail closed when missing/false
- Assumption: helper methods are safe to call before enablement but return documented disabled behavior.

## References

- Epic: `./epic.md`
- Feature design: `../../design/feature-design.md`
- Contract: `../../design/contracts/task-handoff-summary-helpers.md`
