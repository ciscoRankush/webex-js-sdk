# Questionnaire - CAI-7974

## Metadata

| Field | Value |
|---|---|
| Feature / ticket key | CAI-7974 |
| Intake type | feature |
| Source ticket | https://jira-eng-sjc12.cisco.com/jira/browse/CAI-7974 |
| Runtime | Codex generator |
| Created at | 2026-06-24T19:02:22Z |
| Generated from | `feature-intake` / `feature-capture` / `feature-discovery` / `feature-decompose` |

## Source Material

| Source | Access path | Result |
|---|---|---|
| Jira CAI-7974 | Jira SJC12 MCP `call_jira_rest_api` | Read summary, description, status, component, labels, reporter, assignee, comments, links, and attachments. |
| GitHub PR #4794 | GitHub connector `webex/webex-js-sdk#4794` | Read PR metadata and changed filenames/patches. PR is merged and treated as baseline by explicit user approval. |
| Current package SDD | `packages/@webex/contact-center/.sdd/manifest.json`, `AGENTS.md`, `ai-docs/SPEC_INDEX.md`, `ai-docs/ARCHITECTURE.md` | Read module coverage, routing docs, standing architecture, and feature templates. |
| Local source evidence | `packages/@webex/contact-center/src/**` | Current branch lacks PR #4794 merge; user approved treating PR #4794 as prior baseline for this lifecycle run. |

## Intake Transcript

| Question | Answer | Validation status | Evidence |
|---|---|---|---|
| Q1. Are these the modules this feature touches: `contact-center-plugin`, `config`, `task`, `voice-webrtc`, `metrics`, and the prior-work `ApiAIAssistant` service? | Yes. | Verified from Jira, PR #4794, and code routing. | Jira remaining scope names AI Assistant service, feature flags, WebSocket events, task events, and helpers. SDD routes public facade to `contact-center-plugin`, config flags to `config`, WebSocket/task events to `task`, consult/transfer helpers to `voice-webrtc`, and operational signals to `metrics`. |
| Q2. Are the manifest coverage states correct for touched modules? | Yes: all touched modules are `Partial`. | Verified from manifest. | `.sdd/manifest.json` lists `Partial` for `contact-center-plugin`, `config`, `task`, `voice-webrtc`, and `metrics`. |
| Q3. WHAT in one sentence? | Add SDK support for Agent Handoff Summary events and public task APIs so widgets can request, receive, and respond to mid-call summaries during consult/transfer flows. | Verified from Jira. | Jira summary: "SDK: Add Agent Handoff Summary events and public APIs"; remaining scope lists MID_CALL_SUMMARY, response event, task events, and task helpers. |
| Q4. WHY? | A subsequent or consulted agent needs an AI-generated handoff summary at the transfer/consult point without widgets calling backend AI APIs directly or bypassing SDK task state. | Inferred from Jira scope and current SDK architecture. | Jira implementation sequence says request on transfer/consult popup, wait for summary, emit task event, expose payload to widget, and expose response helper. Current architecture exposes task events from `TaskManager` and public operations through `Task`/`Voice`. |
| Q5. Confirm scope and non-empty out-of-scope. | In-scope and out-of-scope are recorded in the intake summary/spec. | Verified from Jira. | Jira says PR #4794 already delivered generic AI Assistant service and transcript plumbing; remaining scope is handoff summary flow only. |
| Q6. Acceptance signals? | The SDK gates summary requests, requests via AI Assistant, handles summary WebSocket messages, emits public task events, exposes request/response helpers, and adds typings/tests/docs. | Verified from Jira. | Jira remaining scope and implementation sequence enumerate these signals. |
| Q7. Change class? | `contract-affecting + security`. | Verified from package contracts and data sensitivity. | New public task helpers/events/types change the published package surface. Summary/transcript-derived payloads can contain sensitive customer conversation data, so security/privacy handling must be explicit. |
| Q8. New or changed Provides/Requires? | Provides adds public task summary events and helpers. Requires existing AI Assistant `/event` transport and WCC WebSocket summary events. | Verified from Jira and PR #4794 prior work. | PR #4794 added `ApiAIAssistant`, AI feature flags, and event-name types; Jira remaining scope reuses that service for handoff summary flows. |
| Q9. New or changed schema/table/migration? | No persistent schema or migration. Runtime event payload/type shapes change only. | Verified from package architecture. | `@webex/contact-center` does not own persistent storage; `ARCHITECTURE.md` states runtime state is in-memory task/config state. |
| Q10. User-visible flow / Figma? | No SDK-owned UI. This supports a widget-visible flow through SDK events/APIs, but UI implementation is out of scope. | Verified from package boundary. | `AGENTS.md` says this package is SDK code, not a backend service or UI product. |
| Q11. Public API change / semver impact? | Yes. Additive public events/helpers/types imply a minor version surface change. | Verified from SDD public surface docs. | `contact-center-plugin` docs require export and SDK manifest updates for public surface changes. |
| Q12. Network API or external dependency change? | Reuses existing AI Assistant `/event` transport from PR #4794; no new external service beyond that baseline. | Verified from Jira and PR #4794. | Jira says "reuse existing ApiAIAssistant service"; PR #4794 added `/event` transport constants/service. |
| Q13. Published/consumed events changed? | Yes. Consume `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`; publish SDK task events for handoff summary delivery. | Verified from Jira. | Jira remaining scope lists these WebSocket events and task event exposure. |

## Decision Log

| Decision | Status | Evidence / rationale |
|---|---|---|
| Treat PR #4794 as baseline even though current checkout is not descended from it. | Approved by user. | User answered "yes" to baseline question. This prevents this feature lifecycle from re-scoping the already-shipped transcript infrastructure. |
| Keep code implementation out of scope for this run. | Approved by user request. | User asked to stop before code implementation. |
| Produce lifecycle files under `packages/@webex/contact-center/features/CAI-7974/`. | Approved by user. | User approved after target file list was summarized. |
| Use one epic with three PR-sized tasks. | Proposed by Codex generator. | The feature is one coherent SDK capability with separate non-overlapping work areas: AI Assistant/config, WebSocket/task event routing, and public task helpers/docs. |

## Section Selection Record - feature-spec for CAI-7974

| Include-if condition | Question | Answer | Evidence | Decision | Rationale |
|---|---|---|---|---|---|
| `feature.feature_nontrivial` | F-1 | Yes | Multiple async surfaces: config gate, AI request, WebSocket event, task event, public helper. | KEEP | Technically non-trivial state/event coordination. |
| `feature.feature_interactions` | F-2 | Yes | Consult/transfer/conference/task states plus feature enablement gates. | KEEP | Behavior varies by task state and runtime flags. |
| `feature.backward_compat` | F-3 | Yes | Additive public API; disabled flag must preserve existing consult/transfer behavior. | KEEP | Existing clients must continue unaffected when disabled or unsupported. |
| `feature.serviceability` | F-4 | Yes | New request/delivery/failure paths need logs/metrics without payload leakage. | KEEP | Operators need observability for summary request failures and delivery. |
| `feature.doc_obligations` | F-5 | Yes | Public SDK helpers/events/types and `sdk-manifest.yaml` must be updated. | KEEP | Published package contract changes require docs/manifest. |
| `feature.cross_package` | F-6 | No | Work is scoped to `packages/@webex/contact-center`; PR #4794 touched sample docs but this feature does not require cross-package work. | DROP | Monorepo exists, but this change is package-local. |
| `feature.new_service` | F-7 | No | Jira says reuse existing `ApiAIAssistant` service. | DROP | No new service/component. |
| `feature.bulk_batch` | F-8 | No | Single interaction/task flow. | DROP | No bulk/CSV/batch behavior. |
| `feature.wire_protocol` | F-9 | No | JSON HTTP/WebSocket contracts change, but no new binary/protocol format. | DROP | Event/API contracts are covered separately. |
| `feature.cicd_changes` | F-10 | No | Existing Jest/unit/lint/build pipeline is sufficient. | DROP | No pipeline change needed. |
| `feature.needs_arch_views` | F-11 | No | One context/block view plus sequence diagram captures the design. | DROP | Additional deployment/data views would be redundant. |
| `feature.needs_sequence_diagrams` | F-12 | Yes | Primary and disabled/error paths are async and event-driven. | KEEP | Sequence view is useful for request, delivery, and response ordering. |
| `feature.changes_events` | F-13 | Yes | Jira names WebSocket event handling and SDK task event exposure. | KEEP | Event contracts change. |
| `feature.changes_api` | derived | Yes | Change class includes `contract-affecting`. | KEEP | Adds/changes SDK/public and AI Assistant event contracts. |
| `feature.changes_public_api` | derived | Yes | Change class includes `contract-affecting`. | KEEP | Public task helpers/events/types change published surface. |
| `feature.nontrivial_interface` | derived | Yes | Change class includes `contract-affecting`. | KEEP | Requires per-interface contract docs. |
| `feature.security_compliance` | derived | Yes | Change class includes `security`. | KEEP | Summary payloads may carry sensitive conversation data. |
| `feature.security_rbac` | derived | Yes | Change class includes `security`. | KEEP | Existing Webex auth and feature enablement gates must be preserved. |
| `feature.touches_data_shapes` | explicit | Yes | New summary request/response payload and task event shapes. | KEEP | Runtime data shapes change even without persistence. |
| `feature.user_visible_surface` | explicit | No | SDK emits data for widget; SDK does not own UI. | DROP | UI flow belongs to consuming widget. |
| `feature.changes_ui` | explicit | No | No in-package UI. | DROP | Not an SDK-owned UI change. |
| `feature.perf_critical` | explicit | No | No throughput/latency target in Jira. | DROP | Guardrails still cover no regressions. |
| `feature.scale_requirements` | explicit | No | No high-volume behavior. | DROP | Single-task event flow. |
| `feature.data_model_changes` | explicit | No | No persistence/schema/cache change. | DROP | Runtime payload types only. |
| `feature.infra_changes` | explicit | No | No infra or deployment change. | DROP | Reuses existing backend services. |

## Design Section Selection Record - CAI-7974

| Include-if condition | Source | Answer | Decision | Rationale |
|---|---|---|---|---|
| `feature.needs_arch_views` | F-11 | No | DROP | Context/block plus sequence view is enough. |
| `feature.scale_requirements` | Capture profile | No | DROP | No scale target. |
| `feature.new_service` | Capture profile | No | DROP | Reuse existing `ApiAIAssistant`. |
| `feature.infra_changes` | Capture profile | No | DROP | No infrastructure change. |
| `feature.cicd_changes` | Capture profile | No | DROP | Existing package CI/test commands apply. |
| `feature.nontrivial_interface` | Capture profile | Yes | KEEP | Per-interface contracts are written under `design/contracts/`. |
| `feature.data_model_changes` | Capture profile | No | DROP | Runtime payload shapes only; no persistence. |
| `feature.security_rbac` | Capture profile | Yes | KEEP | Feature enablement and auth/data handling are security-relevant. |
| `feature.wire_protocol` | Capture profile | No | DROP | No new protocol format. |
| `feature.user_visible_surface` | Capture profile | No | DROP | SDK event/API only. |
| `feature.bulk_batch` | Capture profile | No | DROP | No batch behavior. |
| `feature.needs_sequence_diagrams` | F-12 | Yes | KEEP | Async request/delivery/response paths need ordering clarity. |

## Soft Commit

| Field | Value |
|---|---|
| soft_commit_approved | true |
| approved_by | User approval in Codex chat to run through discovery/design and decomposition |
| approved_at | 2026-06-24T19:02:22Z |
| scope | Documentation lifecycle artifacts only; no code implementation |

## Decomposition Decision Log

| Item | Decision |
|---|---|
| Epic split | One epic: `handoff-summary-sdk-flow`, because the feature is one SDK capability across config, AI Assistant request, WebSocket routing, and task API exposure. |
| Task split | T1 AI Assistant/config enablement, T2 WebSocket/task event delivery, T3 public task helpers/docs/tests. |
| Boundary handling | `src/services/task/types.ts` is shared by T2 and T3 but in distinct sections; T3 is blocked by T2 for final type/export reconciliation. |
| Implementation status | Not started. This run stops before code implementation. |

## Open Questions

| ID | Question | Owner | Status | Blocks |
|---|---|---|---|---|
| Q-1 | Confirm exact backend payload schema for `FEATURE_ENABLEMENT`, `MID_CALL_SUMMARY`, and `MID_CALL_SUMMARY_RESPONSE_SUBSEQUENT_AGENT`. | Backend/API owner | Open | Final contract implementation and tests. |
| Q-2 | Confirm response helper action vocabulary for cancel, consult, and transfer actions. | Product/backend owner | Open | Public helper payload validation and contract doc finalization. |
| Q-3 | Confirm whether `consultTransferSummariesEnabled` should be false-only disabled or require strict true before any request. | Product/backend owner | Open | Gate behavior in T1/T3. |
