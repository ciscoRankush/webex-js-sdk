<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: spec-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Spec Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry). This file is the router; system overview in [`ARCHITECTURE.md`](ARCHITECTURE.md). Load `AGENTS.md` + this file first; pull every other doc on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; route to the minimum needed per task.

> AI agent entry point after `AGENTS.md`. Load this once at session start; pull other docs on demand.
> **Source of truth:** `.sdd/manifest.json` (this file mirrors it for humans). Module specs are generated in
> the host module-spec phase; each `Start here` path is that module's canonical spec target.

## Module Registry
| Module | Responsibility | Manifest coverage state | Start here |
|---|---|---|---|
| `packages/@webex/common/` | Shared utilities/mixins | Partial | `packages/@webex/common/ai-docs/common-spec.md` |
| `packages/@webex/common-evented/` | Evented mixin utility | Partial | `packages/@webex/common-evented/ai-docs/common-evented-spec.md` |
| `packages/@webex/common-timers/` | Timer utilities | Partial | `packages/@webex/common-timers/ai-docs/common-timers-spec.md` |
| `packages/@webex/contact-center/` | Contact-center agent/task SDK | Partial | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| `packages/@webex/helper-html/` | HTML sanitization helper | Partial | `packages/@webex/helper-html/ai-docs/helper-html-spec.md` |
| `packages/@webex/helper-image/` | Image processing helper | Partial | `packages/@webex/helper-image/ai-docs/helper-image-spec.md` |
| `packages/@webex/http-core/` | HTTP request/interceptor primitive | Partial | `packages/@webex/http-core/ai-docs/http-core-spec.md` |
| `packages/@webex/internal-plugin-ai-assistant/` | Internal AI-assistant plugin | Partial | `packages/@webex/internal-plugin-ai-assistant/ai-docs/internal-plugin-ai-assistant-spec.md` |
| `packages/@webex/internal-plugin-avatar/` | Internal avatar plugin | Partial | `packages/@webex/internal-plugin-avatar/ai-docs/internal-plugin-avatar-spec.md` |
| `packages/@webex/internal-plugin-board/` | Internal whiteboard plugin | Partial | `packages/@webex/internal-plugin-board/ai-docs/internal-plugin-board-spec.md` |
| `packages/@webex/internal-plugin-calendar/` | Internal calendar plugin | Partial | `packages/@webex/internal-plugin-calendar/ai-docs/internal-plugin-calendar-spec.md` |
| `packages/@webex/internal-plugin-call-ai-summary/` | Internal call AI-summary plugin | Partial | `packages/@webex/internal-plugin-call-ai-summary/ai-docs/internal-plugin-call-ai-summary-spec.md` |
| `packages/@webex/internal-plugin-conversation/` | Internal conversation plugin | Partial | `packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md` |
| `packages/@webex/internal-plugin-device/` | Internal device-registration plugin | Partial | `packages/@webex/internal-plugin-device/ai-docs/internal-plugin-device-spec.md` |
| `packages/@webex/internal-plugin-dss/` | Internal directory-search-service plugin | Partial | `packages/@webex/internal-plugin-dss/ai-docs/internal-plugin-dss-spec.md` |
| `packages/@webex/internal-plugin-ediscovery/` | Internal eDiscovery plugin | Partial | `packages/@webex/internal-plugin-ediscovery/ai-docs/internal-plugin-ediscovery-spec.md` |
| `packages/@webex/internal-plugin-encryption/` | Internal encryption plugin | Partial | `packages/@webex/internal-plugin-encryption/ai-docs/internal-plugin-encryption-spec.md` |
| `packages/@webex/internal-plugin-feature/` | Internal feature-toggle plugin | Partial | `packages/@webex/internal-plugin-feature/ai-docs/internal-plugin-feature-spec.md` |
| `packages/@webex/internal-plugin-flag/` | Internal message-flag plugin | Partial | `packages/@webex/internal-plugin-flag/ai-docs/internal-plugin-flag-spec.md` |
| `packages/@webex/internal-plugin-llm/` | Internal LLM websocket plugin | Partial | `packages/@webex/internal-plugin-llm/ai-docs/internal-plugin-llm-spec.md` |
| `packages/@webex/internal-plugin-locus/` | Internal Locus meeting-state plugin | Partial | `packages/@webex/internal-plugin-locus/ai-docs/internal-plugin-locus-spec.md` |
| `packages/@webex/internal-plugin-lyra/` | Internal Lyra device plugin | Partial | `packages/@webex/internal-plugin-lyra/ai-docs/internal-plugin-lyra-spec.md` |
| `packages/@webex/internal-plugin-mercury/` | Internal Mercury websocket plugin | Partial | `packages/@webex/internal-plugin-mercury/ai-docs/internal-plugin-mercury-spec.md` |
| `packages/@webex/internal-plugin-metrics/` | Internal metrics plugin | Partial | `packages/@webex/internal-plugin-metrics/ai-docs/internal-plugin-metrics-spec.md` |
| `packages/@webex/internal-plugin-presence/` | Internal presence plugin | Partial | `packages/@webex/internal-plugin-presence/ai-docs/internal-plugin-presence-spec.md` |
| `packages/@webex/internal-plugin-scheduler/` | Internal scheduler plugin | Partial | `packages/@webex/internal-plugin-scheduler/ai-docs/internal-plugin-scheduler-spec.md` |
| `packages/@webex/internal-plugin-search/` | Internal search plugin | Partial | `packages/@webex/internal-plugin-search/ai-docs/internal-plugin-search-spec.md` |
| `packages/@webex/internal-plugin-support/` | Internal support/feedback plugin | Partial | `packages/@webex/internal-plugin-support/ai-docs/internal-plugin-support-spec.md` |
| `packages/@webex/internal-plugin-task/` | Internal task plugin | Partial | `packages/@webex/internal-plugin-task/ai-docs/internal-plugin-task-spec.md` |
| `packages/@webex/internal-plugin-team/` | Internal team plugin | Partial | `packages/@webex/internal-plugin-team/ai-docs/internal-plugin-team-spec.md` |
| `packages/@webex/internal-plugin-user/` | Internal user plugin | Partial | `packages/@webex/internal-plugin-user/ai-docs/internal-plugin-user-spec.md` |
| `packages/@webex/internal-plugin-voicea/` | Internal Voicea transcription plugin | Partial | `packages/@webex/internal-plugin-voicea/ai-docs/internal-plugin-voicea-spec.md` |
| `packages/@webex/internal-plugin-wdm/` | Internal web-device-manager plugin | Partial | `packages/@webex/internal-plugin-wdm/ai-docs/internal-plugin-wdm-spec.md` |
| `packages/@webex/jsdoctrinetest/` | JSDoc-based test helper | Partial | `packages/@webex/jsdoctrinetest/ai-docs/jsdoctrinetest-spec.md` |
| `packages/@webex/media-helpers/` | Media/WebRTC helpers | Partial | `packages/@webex/media-helpers/ai-docs/media-helpers-spec.md` |
| `packages/@webex/plugin-attachment-actions/` | Attachment-actions plugin | Partial | `packages/@webex/plugin-attachment-actions/ai-docs/plugin-attachment-actions-spec.md` |
| `packages/@webex/plugin-authorization/` | Authorization auto-loader plugin | Partial | `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` |
| `packages/@webex/plugin-authorization-browser/` | Browser OAuth plugin | Partial | `packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md` |
| `packages/@webex/plugin-authorization-browser-first-party/` | First-party browser OAuth plugin | Partial | `packages/@webex/plugin-authorization-browser-first-party/ai-docs/plugin-authorization-browser-first-party-spec.md` |
| `packages/@webex/plugin-authorization-node/` | Node OAuth plugin | Partial | `packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md` |
| `packages/@webex/plugin-device-manager/` | Device-manager plugin | Partial | `packages/@webex/plugin-device-manager/ai-docs/plugin-device-manager-spec.md` |
| `packages/@webex/plugin-encryption/` | Public encryption plugin | Partial | `packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md` |
| `packages/@webex/plugin-logger/` | Logger plugin | Partial | `packages/@webex/plugin-logger/ai-docs/plugin-logger-spec.md` |
| `packages/@webex/plugin-meetings/` | Meetings/Locus/media orchestration | Partial | `packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md` |
| `packages/@webex/plugin-memberships/` | Room-memberships plugin | Partial | `packages/@webex/plugin-memberships/ai-docs/plugin-memberships-spec.md` |
| `packages/@webex/plugin-messages/` | Messages plugin | Partial | `packages/@webex/plugin-messages/ai-docs/plugin-messages-spec.md` |
| `packages/@webex/plugin-people/` | People plugin | Partial | `packages/@webex/plugin-people/ai-docs/plugin-people-spec.md` |
| `packages/@webex/plugin-presence/` | Public presence plugin | Partial | `packages/@webex/plugin-presence/ai-docs/plugin-presence-spec.md` |
| `packages/@webex/plugin-rooms/` | Rooms plugin | Partial | `packages/@webex/plugin-rooms/ai-docs/plugin-rooms-spec.md` |
| `packages/@webex/plugin-team-memberships/` | Team-memberships plugin | Partial | `packages/@webex/plugin-team-memberships/ai-docs/plugin-team-memberships-spec.md` |
| `packages/@webex/plugin-teams/` | Teams plugin | Partial | `packages/@webex/plugin-teams/ai-docs/plugin-teams-spec.md` |
| `packages/@webex/plugin-webhooks/` | Webhooks plugin | Partial | `packages/@webex/plugin-webhooks/ai-docs/plugin-webhooks-spec.md` |
| `packages/@webex/recipe-private-web-client/` | Private web-client recipe bundle | Partial | `packages/@webex/recipe-private-web-client/ai-docs/recipe-private-web-client-spec.md` |
| `packages/@webex/storage-adapter-local-forage/` | localForage storage adapter | Partial | `packages/@webex/storage-adapter-local-forage/ai-docs/storage-adapter-local-forage-spec.md` |
| `packages/@webex/storage-adapter-local-storage/` | localStorage storage adapter | Partial | `packages/@webex/storage-adapter-local-storage/ai-docs/storage-adapter-local-storage-spec.md` |
| `packages/@webex/storage-adapter-session-storage/` | sessionStorage storage adapter | Partial | `packages/@webex/storage-adapter-session-storage/ai-docs/storage-adapter-session-storage-spec.md` |
| `packages/@webex/storage-adapter-spec/` | Storage-adapter conformance suite | Partial | `packages/@webex/storage-adapter-spec/ai-docs/storage-adapter-spec-spec.md` |
| `packages/@webex/test-helper-appid/` | AppID test helper | Partial | `packages/@webex/test-helper-appid/ai-docs/test-helper-appid-spec.md` |
| `packages/@webex/test-helper-automation/` | Automation test helper | Partial | `packages/@webex/test-helper-automation/ai-docs/test-helper-automation-spec.md` |
| `packages/@webex/test-helper-chai/` | Chai assertion test helper | Partial | `packages/@webex/test-helper-chai/ai-docs/test-helper-chai-spec.md` |
| `packages/@webex/test-helper-file/` | File-fixture test helper | Partial | `packages/@webex/test-helper-file/ai-docs/test-helper-file-spec.md` |
| `packages/@webex/test-helper-make-local-url/` | Local-URL test helper | Partial | `packages/@webex/test-helper-make-local-url/ai-docs/test-helper-make-local-url-spec.md` |
| `packages/@webex/test-helper-mocha/` | Mocha test helper | Partial | `packages/@webex/test-helper-mocha/ai-docs/test-helper-mocha-spec.md` |
| `packages/@webex/test-helper-mock-web-socket/` | Mock-websocket test helper | Partial | `packages/@webex/test-helper-mock-web-socket/ai-docs/test-helper-mock-web-socket-spec.md` |
| `packages/@webex/test-helper-mock-webex/` | Mock-webex test helper | Partial | `packages/@webex/test-helper-mock-webex/ai-docs/test-helper-mock-webex-spec.md` |
| `packages/@webex/test-helper-refresh-callback/` | Refresh-callback test helper | Partial | `packages/@webex/test-helper-refresh-callback/ai-docs/test-helper-refresh-callback-spec.md` |
| `packages/@webex/test-helper-retry/` | Retry test helper | Partial | `packages/@webex/test-helper-retry/ai-docs/test-helper-retry-spec.md` |
| `packages/@webex/test-helper-server/` | Test-server helper | Partial | `packages/@webex/test-helper-server/ai-docs/test-helper-server-spec.md` |
| `packages/@webex/test-helper-test-users/` | Test-users helper | Partial | `packages/@webex/test-helper-test-users/ai-docs/test-helper-test-users-spec.md` |
| `packages/@webex/test-users/` | Test-users provisioning | Partial | `packages/@webex/test-users/ai-docs/test-users-spec.md` |
| `packages/@webex/test-webex-node/` | Node integration test harness | Partial | `packages/@webex/test-webex-node/ai-docs/test-webex-node-spec.md` |
| `packages/@webex/webex-core/` | Plugin registry, HTTP pipeline, storage, events | Partial | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| `packages/@webex/webex-server/` | Server-side webex bootstrap | Partial | `packages/@webex/webex-server/ai-docs/webex-server-spec.md` |
| `packages/@webex/webrtc/` | WebRTC wrapper | Partial | `packages/@webex/webrtc/ai-docs/webrtc-spec.md` |
| `packages/@webex/xunit-with-logs/` | xUnit-with-logs reporter | Partial | `packages/@webex/xunit-with-logs/ai-docs/xunit-with-logs-spec.md` |
| `packages/byods/` | Bring-Your-Own-Device-Server SDK | Partial | `packages/byods/ai-docs/byods-spec.md` |
| `packages/byods-demo-server/` | BYoDS demo server | Partial | `packages/byods-demo-server/ai-docs/byods-demo-server-spec.md` |
| `packages/calling/` | Standalone calling SDK | Partial | `packages/calling/ai-docs/calling-spec.md` |
| `packages/config/api-extractor/` | Shared API-Extractor config | Partial | `packages/config/api-extractor/ai-docs/api-extractor-spec.md` |
| `packages/config/esbuild/` | Shared esbuild config | Partial | `packages/config/esbuild/ai-docs/esbuild-spec.md` |
| `packages/config/eslint/` | Shared ESLint config | Partial | `packages/config/eslint/ai-docs/eslint-spec.md` |
| `packages/config/jasmine/` | Shared Jasmine config | Partial | `packages/config/jasmine/ai-docs/jasmine-spec.md` |
| `packages/config/nyc/` | Shared nyc coverage config | Partial | `packages/config/nyc/ai-docs/nyc-spec.md` |
| `packages/config/typescript/` | Shared TypeScript config | Partial | `packages/config/typescript/ai-docs/typescript-spec.md` |
| `packages/legacy/babel/` | Legacy Babel config | Partial | `packages/legacy/babel/ai-docs/babel-spec.md` |
| `packages/legacy/env/` | Legacy env config | Partial | `packages/legacy/env/ai-docs/env-spec.md` |
| `packages/legacy/eslint/` | Legacy ESLint config | Partial | `packages/legacy/eslint/ai-docs/eslint-spec.md` |
| `packages/legacy/jest/` | Legacy Jest config | Partial | `packages/legacy/jest/ai-docs/jest-spec.md` |
| `packages/legacy/tools/` | Legacy build tools | Partial | `packages/legacy/tools/ai-docs/tools-spec.md` |
| `packages/tools/cli/` | CLI tooling | Partial | `packages/tools/cli/ai-docs/cli-spec.md` |
| `packages/tools/package/` | Package-tools | Partial | `packages/tools/package/ai-docs/package-spec.md` |
| `packages/webex/` | Primary unified webex module | Partial | `packages/webex/ai-docs/webex-spec.md` |
| `packages/webex-node/` | Node unified webex module | Partial | `packages/webex-node/ai-docs/webex-node-spec.md` |

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
| INC id | Date | Module | One-line | Link |
|---|---|---|---|---|
| — | — | — | No incidents recorded during onboarding | — |

## Phase-Based Loading Protocol
<!-- Kept: large monorepo (93 modules) — on-demand loading is worthwhile -->
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
| Contracts | `CONTRACTS.md` | root index of public-surface contracts; details at owning modules |
| Service state | `SERVICE_STATE.md` | living as-built registry |
| Test index | `TEST_INDEX.md` | test tiers, canonical commands, locations, frameworks, gates |
| Getting started | `GETTING_STARTED.md` | clone/build/run + workspace layout |
| Review catalog | `REVIEW_CHECKLIST.md` | core + coverage + cross-cutting review checks |
