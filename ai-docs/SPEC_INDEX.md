<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: spec-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->

# Spec Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry). This file is the router (generated at `ai-docs/SPEC_INDEX.md`); system overview in [`ARCHITECTURE.md`](ARCHITECTURE.md). Load `AGENTS.md` + this file first; pull every other doc on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; route to the minimum needed per task.

> AI agent entry point after `AGENTS.md`. Load this once at session start; pull other docs on demand.
> **Source of truth:** `.sdd/manifest.json` (this file mirrors it for humans).

## Module Registry
<!-- One row per manifest module; this is a flat workspace (no module nesting), so no depth prefixes are used. -->
| Module | Responsibility | Manifest coverage state | Start here |
|---|---|---|---|
| `packages/@webex/common/` | Shared base mixins/decorators/utilities | Partial | `packages/@webex/common/ai-docs/common-spec.md` |
| `packages/@webex/common-evented/` | Evented base-class helper | Partial | `packages/@webex/common-evented/ai-docs/common-evented-spec.md` |
| `packages/@webex/common-timers/` | Safe timer wrappers | Partial | `packages/@webex/common-timers/ai-docs/common-timers-spec.md` |
| `packages/@webex/contact-center/` | Webex Contact Center SDK plugin | Partial | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| `packages/@webex/helper-html/` | HTML sanitization/filtering helper | Partial | `packages/@webex/helper-html/ai-docs/helper-html-spec.md` |
| `packages/@webex/helper-image/` | Image orientation/thumbnail helper | Partial | `packages/@webex/helper-image/ai-docs/helper-image-spec.md` |
| `packages/@webex/http-core/` | HTTP request/interceptor core | Partial | `packages/@webex/http-core/ai-docs/http-core-spec.md` |
| `packages/@webex/internal-plugin-ai-assistant/` | Internal AI-assistant plugin | Partial | `packages/@webex/internal-plugin-ai-assistant/ai-docs/internal-plugin-ai-assistant-spec.md` |
| `packages/@webex/internal-plugin-avatar/` | Internal avatar-fetch plugin | Partial | `packages/@webex/internal-plugin-avatar/ai-docs/internal-plugin-avatar-spec.md` |
| `packages/@webex/internal-plugin-board/` | Internal whiteboard/board plugin | Partial | `packages/@webex/internal-plugin-board/ai-docs/internal-plugin-board-spec.md` |
| `packages/@webex/internal-plugin-calendar/` | Internal calendar plugin | Partial | `packages/@webex/internal-plugin-calendar/ai-docs/internal-plugin-calendar-spec.md` |
| `packages/@webex/internal-plugin-call-ai-summary/` | Internal call AI-summary plugin | Partial | `packages/@webex/internal-plugin-call-ai-summary/ai-docs/internal-plugin-call-ai-summary-spec.md` |
| `packages/@webex/internal-plugin-conversation/` | Encrypted conversation/activity plugin | Partial | `packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md` |
| `packages/@webex/internal-plugin-device/` | Device-registration plugin | Partial | `packages/@webex/internal-plugin-device/ai-docs/internal-plugin-device-spec.md` |
| `packages/@webex/internal-plugin-dss/` | Directory-search-service plugin | Partial | `packages/@webex/internal-plugin-dss/ai-docs/internal-plugin-dss-spec.md` |
| `packages/@webex/internal-plugin-ediscovery/` | Ediscovery/compliance plugin | Partial | `packages/@webex/internal-plugin-ediscovery/ai-docs/internal-plugin-ediscovery-spec.md` |
| `packages/@webex/internal-plugin-encryption/` | KMS/encryption plugin | Partial | `packages/@webex/internal-plugin-encryption/ai-docs/internal-plugin-encryption-spec.md` |
| `packages/@webex/internal-plugin-feature/` | Feature-toggle plugin | Partial | `packages/@webex/internal-plugin-feature/ai-docs/internal-plugin-feature-spec.md` |
| `packages/@webex/internal-plugin-flag/` | Message-flagging plugin | Partial | `packages/@webex/internal-plugin-flag/ai-docs/internal-plugin-flag-spec.md` |
| `packages/@webex/internal-plugin-llm/` | LLM websocket plugin | Partial | `packages/@webex/internal-plugin-llm/ai-docs/internal-plugin-llm-spec.md` |
| `packages/@webex/internal-plugin-locus/` | Locus meeting control-plane plugin | Partial | `packages/@webex/internal-plugin-locus/ai-docs/internal-plugin-locus-spec.md` |
| `packages/@webex/internal-plugin-lyra/` | Lyra workspace/device pairing plugin | Partial | `packages/@webex/internal-plugin-lyra/ai-docs/internal-plugin-lyra-spec.md` |
| `packages/@webex/internal-plugin-mercury/` | Mercury websocket transport plugin | Partial | `packages/@webex/internal-plugin-mercury/ai-docs/internal-plugin-mercury-spec.md` |
| `packages/@webex/internal-plugin-metrics/` | Metrics/telemetry plugin | Partial | `packages/@webex/internal-plugin-metrics/ai-docs/internal-plugin-metrics-spec.md` |
| `packages/@webex/internal-plugin-presence/` | Presence plugin (internal) | Partial | `packages/@webex/internal-plugin-presence/ai-docs/internal-plugin-presence-spec.md` |
| `packages/@webex/internal-plugin-scheduler/` | Meeting-scheduler plugin | Partial | `packages/@webex/internal-plugin-scheduler/ai-docs/internal-plugin-scheduler-spec.md` |
| `packages/@webex/internal-plugin-search/` | Search plugin | Partial | `packages/@webex/internal-plugin-search/ai-docs/internal-plugin-search-spec.md` |
| `packages/@webex/internal-plugin-support/` | Support/feedback/log-upload plugin | Partial | `packages/@webex/internal-plugin-support/ai-docs/internal-plugin-support-spec.md` |
| `packages/@webex/internal-plugin-task/` | Task plugin (internal) | Partial | `packages/@webex/internal-plugin-task/ai-docs/internal-plugin-task-spec.md` |
| `packages/@webex/internal-plugin-team/` | Team plugin (internal) | Partial | `packages/@webex/internal-plugin-team/ai-docs/internal-plugin-team-spec.md` |
| `packages/@webex/internal-plugin-user/` | User-management plugin | Partial | `packages/@webex/internal-plugin-user/ai-docs/internal-plugin-user-spec.md` |
| `packages/@webex/internal-plugin-voicea/` | Voicea transcription/voice-assistant plugin | Partial | `packages/@webex/internal-plugin-voicea/ai-docs/internal-plugin-voicea-spec.md` |
| `packages/@webex/internal-plugin-wdm/` | Web Device Manager plugin | Partial | `packages/@webex/internal-plugin-wdm/ai-docs/internal-plugin-wdm-spec.md` |
| `packages/@webex/jsdoctrinetest/` | JSDoc doctrine test tooling | Partial | `packages/@webex/jsdoctrinetest/ai-docs/jsdoctrinetest-spec.md` |
| `packages/@webex/media-helpers/` | WebRTC media helper package | Partial | `packages/@webex/media-helpers/ai-docs/media-helpers-spec.md` |
| `packages/@webex/plugin-attachment-actions/` | Attachment-actions (adaptive cards) plugin | Partial | `packages/@webex/plugin-attachment-actions/ai-docs/plugin-attachment-actions-spec.md` |
| `packages/@webex/plugin-authorization/` | OAuth authorization plugin | Partial | `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` |
| `packages/@webex/plugin-authorization-browser/` | Browser OAuth authorization plugin | Partial | `packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md` |
| `packages/@webex/plugin-authorization-browser-first-party/` | First-party browser OAuth plugin | Partial | `packages/@webex/plugin-authorization-browser-first-party/ai-docs/plugin-authorization-browser-first-party-spec.md` |
| `packages/@webex/plugin-authorization-node/` | Node OAuth authorization plugin | Partial | `packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md` |
| `packages/@webex/plugin-device-manager/` | Device-manager plugin | Partial | `packages/@webex/plugin-device-manager/ai-docs/plugin-device-manager-spec.md` |
| `packages/@webex/plugin-encryption/` | Encryption plugin (public) | Partial | `packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md` |
| `packages/@webex/plugin-logger/` | Logger plugin | Partial | `packages/@webex/plugin-logger/ai-docs/plugin-logger-spec.md` |
| `packages/@webex/plugin-meetings/` | Meetings SDK plugin | Partial | `packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md` |
| `packages/@webex/plugin-memberships/` | Room-memberships plugin | Partial | `packages/@webex/plugin-memberships/ai-docs/plugin-memberships-spec.md` |
| `packages/@webex/plugin-messages/` | Messages plugin | Partial | `packages/@webex/plugin-messages/ai-docs/plugin-messages-spec.md` |
| `packages/@webex/plugin-people/` | People plugin | Partial | `packages/@webex/plugin-people/ai-docs/plugin-people-spec.md` |
| `packages/@webex/plugin-presence/` | Presence plugin (public) | Partial | `packages/@webex/plugin-presence/ai-docs/plugin-presence-spec.md` |
| `packages/@webex/plugin-rooms/` | Rooms plugin | Partial | `packages/@webex/plugin-rooms/ai-docs/plugin-rooms-spec.md` |
| `packages/@webex/plugin-team-memberships/` | Team-memberships plugin | Partial | `packages/@webex/plugin-team-memberships/ai-docs/plugin-team-memberships-spec.md` |
| `packages/@webex/plugin-teams/` | Teams plugin | Partial | `packages/@webex/plugin-teams/ai-docs/plugin-teams-spec.md` |
| `packages/@webex/plugin-webhooks/` | Webhooks plugin | Partial | `packages/@webex/plugin-webhooks/ai-docs/plugin-webhooks-spec.md` |
| `packages/@webex/recipe-private-web-client/` | Private web client recipe package | Partial | `packages/@webex/recipe-private-web-client/ai-docs/recipe-private-web-client-spec.md` |
| `packages/@webex/storage-adapter-local-forage/` | localForage storage adapter | Partial | `packages/@webex/storage-adapter-local-forage/ai-docs/storage-adapter-local-forage-spec.md` |
| `packages/@webex/storage-adapter-local-storage/` | localStorage storage adapter | Partial | `packages/@webex/storage-adapter-local-storage/ai-docs/storage-adapter-local-storage-spec.md` |
| `packages/@webex/storage-adapter-session-storage/` | sessionStorage storage adapter | Partial | `packages/@webex/storage-adapter-session-storage/ai-docs/storage-adapter-session-storage-spec.md` |
| `packages/@webex/storage-adapter-spec/` | Storage-adapter conformance spec/suite | Partial | `packages/@webex/storage-adapter-spec/ai-docs/storage-adapter-spec-spec.md` |
| `packages/@webex/test-helper-appid/` | Test helper: app-id/JWT | Partial | `packages/@webex/test-helper-appid/ai-docs/test-helper-appid-spec.md` |
| `packages/@webex/test-helper-automation/` | Test helper: browser automation | Partial | `packages/@webex/test-helper-automation/ai-docs/test-helper-automation-spec.md` |
| `packages/@webex/test-helper-chai/` | Test helper: chai assertions | Partial | `packages/@webex/test-helper-chai/ai-docs/test-helper-chai-spec.md` |
| `packages/@webex/test-helper-file/` | Test helper: file fixtures | Partial | `packages/@webex/test-helper-file/ai-docs/test-helper-file-spec.md` |
| `packages/@webex/test-helper-make-local-url/` | Test helper: local URLs | Partial | `packages/@webex/test-helper-make-local-url/ai-docs/test-helper-make-local-url-spec.md` |
| `packages/@webex/test-helper-mocha/` | Test helper: mocha lifecycle | Partial | `packages/@webex/test-helper-mocha/ai-docs/test-helper-mocha-spec.md` |
| `packages/@webex/test-helper-mock-web-socket/` | Test helper: mock WebSocket | Partial | `packages/@webex/test-helper-mock-web-socket/ai-docs/test-helper-mock-web-socket-spec.md` |
| `packages/@webex/test-helper-mock-webex/` | Test helper: mock Webex instance | Partial | `packages/@webex/test-helper-mock-webex/ai-docs/test-helper-mock-webex-spec.md` |
| `packages/@webex/test-helper-refresh-callback/` | Test helper: token refresh callbacks | Partial | `packages/@webex/test-helper-refresh-callback/ai-docs/test-helper-refresh-callback-spec.md` |
| `packages/@webex/test-helper-retry/` | Test helper: retry semantics | Partial | `packages/@webex/test-helper-retry/ai-docs/test-helper-retry-spec.md` |
| `packages/@webex/test-helper-server/` | Test helper: local fixture server | Partial | `packages/@webex/test-helper-server/ai-docs/test-helper-server-spec.md` |
| `packages/@webex/test-helper-test-users/` | Test helper: test-user provisioning | Partial | `packages/@webex/test-helper-test-users/ai-docs/test-helper-test-users-spec.md` |
| `packages/@webex/test-users/` | Test-user provisioning library | Partial | `packages/@webex/test-users/ai-docs/test-users-spec.md` |
| `packages/@webex/test-webex-node/` | Node test harness for aggregate SDK | Partial | `packages/@webex/test-webex-node/ai-docs/test-webex-node-spec.md` |
| `packages/@webex/webex-core/` | Plugin framework, credentials, services, request stack | Partial | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| `packages/@webex/webex-server/` | Server-side Webex composition | Partial | `packages/@webex/webex-server/ai-docs/webex-server-spec.md` |
| `packages/@webex/webrtc/` | WebRTC wrapper package | Partial | `packages/@webex/webrtc/ai-docs/webrtc-spec.md` |
| `packages/@webex/xunit-with-logs/` | xUnit reporter with attached logs | Partial | `packages/@webex/xunit-with-logs/ai-docs/xunit-with-logs-spec.md` |
| `packages/byods/` | Bring-Your-Own-Device-Service SDK | Partial | `packages/byods/ai-docs/byods-spec.md` |
| `packages/byods-demo-server/` | BYoDS demo server | Partial | `packages/byods-demo-server/ai-docs/byods-demo-server-spec.md` |
| `packages/calling/` | Calling SDK | Partial | `packages/calling/ai-docs/calling-spec.md` |
| `packages/config/api-extractor/` | Shared api-extractor config | Partial | `packages/config/api-extractor/ai-docs/api-extractor-spec.md` |
| `packages/config/esbuild/` | Shared esbuild config | Partial | `packages/config/esbuild/ai-docs/esbuild-spec.md` |
| `packages/config/eslint/` | Shared ESLint config | Partial | `packages/config/eslint/ai-docs/eslint-spec.md` |
| `packages/config/jasmine/` | Shared Jasmine config | Partial | `packages/config/jasmine/ai-docs/jasmine-spec.md` |
| `packages/config/nyc/` | Shared nyc/coverage config | Partial | `packages/config/nyc/ai-docs/nyc-spec.md` |
| `packages/config/typescript/` | Shared TypeScript config | Partial | `packages/config/typescript/ai-docs/typescript-spec.md` |
| `packages/legacy/babel/` | Legacy Babel config/tooling | Partial | `packages/legacy/babel/ai-docs/babel-spec.md` |
| `packages/legacy/env/` | Legacy env config | Partial | `packages/legacy/env/ai-docs/env-spec.md` |
| `packages/legacy/eslint/` | Legacy ESLint config | Partial | `packages/legacy/eslint/ai-docs/eslint-spec.md` |
| `packages/legacy/jest/` | Legacy Jest config | Partial | `packages/legacy/jest/ai-docs/jest-spec.md` |
| `packages/legacy/tools/` | Legacy build/tooling | Partial | `packages/legacy/tools/ai-docs/tools-spec.md` |
| `packages/tools/cli/` | CLI tooling package | Partial | `packages/tools/cli/ai-docs/cli-spec.md` |
| `packages/tools/package/` | Package build/version/publish tooling | Partial | `packages/tools/package/ai-docs/package-spec.md` |
| `packages/webex/` | Aggregate SDK (browser/UMD entry) | Partial | `packages/webex/ai-docs/webex-spec.md` |
| `packages/webex-node/` | Aggregate SDK (node entry) | Partial | `packages/webex-node/ai-docs/webex-node-spec.md` |

## Task Routing
| If the task is… | Load |
|---|---|
| Understanding the system | `ARCHITECTURE.md` |
| Working in `<module>` | `<module-path>/ai-docs/<module-name>-spec.md` |
| A cross-service contract change | `CONTRACTS.md` + `ARCHITECTURE.md` interaction section |
| Running or changing tests | `TEST_INDEX.md` + the affected module spec |
| Updating docs after a code change | affected module specs + relevant standing indexes/contracts |
| Migrating existing specs | manifest source routes + affected module specs + source-fidelity report |

## Incident History
<!-- No repo-tracked incident RCAs were available from committed evidence in this assess-only turn. -->
| INC id | Date | Module | One-line | Link |
|---|---|---|---|---|
| — | — | — | No incident RCAs available from committed evidence | `[NEEDS HUMAN INPUT]` |

## Phase-Based Loading Protocol
| Phase | Load |
|---|---|
| Orient | AGENTS.md + this file |
| Specify | relevant module docs and routed source specs |
| Build | the selected module SPEC(s) + patterns/rules |
| Verify | independent validation |

## Spec Registry
| Doc | Location | Purpose |
|---|---|---|
| Rules | `RULES.md` | enforceable do/don't beyond AGENTS.md critical rules |
| Glossary | `GLOSSARY.md` | ubiquitous language: term → definition → code location |
| Security | `SECURITY.md` | trust boundaries, authn/authz, secret handling, data classification |
| Contracts | `CONTRACTS.md` | root index of public-surface contracts; details live at owning modules or native contract sources |
| Service state | `SERVICE_STATE.md` | living as-built registry — read first to avoid duplicate/breaking surfaces |
| Test index | `TEST_INDEX.md` | test tiers, canonical commands, locations, frameworks, dependencies, and quality gates |
| Getting started | `GETTING_STARTED.md` | clone/build/run + workspace layout |
| Review catalog | `REVIEW_CHECKLIST.md` | the 6-core + 4-coverage + 3-cross-cutting review checks |
