<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
-->

# SPEC_INDEX.md — webex-js-sdk

> Router: load this + `../AGENTS.md` first. Pull module specs on demand.
> Topology: Yarn workspace monorepo (Multi-repo-workspace) · Mode: brownfield · Assess-only bootstrap

## Module Registry

Modules with `migrate-existing` source policy have canonical specs generated from existing source material. All other modules are `Untracked` — specs will be created in a subsequent `doc-backfill` pass.

| Module | Responsibility | Coverage | Start here |
|---|---|---|---|
| `packages/webex/` | Primary SDK npm entry (`webex`); aggregates all plugins | Partial | [`packages/webex/ai-docs/webex-spec.md`](../packages/webex/ai-docs/webex-spec.md) |
| `packages/webex-node/` | Node.js-specific SDK entry (`webex-node` npm package); Node.js-only, no browser support | Partial | [`packages/webex-node/ai-docs/webex-node-spec.md`](../packages/webex-node/ai-docs/webex-node-spec.md) |
| `packages/@webex/webex-core/` | Plugin host, HTTP pipeline, credentials, storage, events, service catalog | Partial | [`packages/@webex/webex-core/ai-docs/webex-core-spec.md`](../packages/@webex/webex-core/ai-docs/webex-core-spec.md) |
| `packages/@webex/http-core/` | Low-level HTTP core, interceptors, browser/Node shims | Untracked | No canonical spec (Untracked) |
| `packages/@webex/common/` | Shared utilities | Untracked | No canonical spec (Untracked) |
| `packages/@webex/common-evented/` | Event mixin utilities | Untracked | No canonical spec (Untracked) |
| `packages/@webex/common-timers/` | Timer abstraction | Untracked | No canonical spec (Untracked) |
| `packages/calling/` | Calling SDK: CallingClient, CallHistory, CallSettings, Voicemail, Contacts, XState FSM | Partial | [`packages/calling/ai-docs/calling-spec.md`](../packages/calling/ai-docs/calling-spec.md) |
| `packages/byods/` | Bring Your Own Data Store SDK | Untracked | No canonical spec (Untracked) |
| `packages/byods-demo-server/` | Demo server for BYODS | Untracked | No canonical spec (Untracked) |
| `packages/@webex/contact-center/` | Contact Center SDK: agent lifecycle, tasks, WebRTC, events | Partial | [`packages/@webex/contact-center/ai-docs/contact-center-spec.md`](../packages/@webex/contact-center/ai-docs/contact-center-spec.md) |
| `packages/@webex/plugin-meetings/` | Meetings SDK: WebRTC, ROAP, Locus, breakouts, transcription, reactions | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-authorization/` | OAuth flow orchestration, environment detection | Partial | [`packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md`](../packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md) |
| `packages/@webex/plugin-authorization-browser/` | Browser OAuth: implicit grant, authcode, CSRF, token storage | Partial | [`packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md`](../packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md) |
| `packages/@webex/plugin-authorization-browser-first-party/` | First-party browser authorization | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-authorization-node/` | Node.js OAuth: authcode, JWT, client credentials | Partial | [`packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md`](../packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md) |
| `packages/@webex/plugin-encryption/` | E2E encryption, KMS, file decrypt | Partial | [`packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md`](../packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md) |
| `packages/@webex/plugin-messages/` | Messaging API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-rooms/` | Spaces/Rooms API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-memberships/` | Room membership API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-people/` | People/identity API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-presence/` | Presence status API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-teams/` | Teams API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-team-memberships/` | Team membership API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-webhooks/` | Webhooks management API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-attachment-actions/` | Card/attachment actions API | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-device-manager/` | Device pairing and management | Untracked | No canonical spec (Untracked) |
| `packages/@webex/plugin-logger/` | SDK-wide logging plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-mercury/` | WebSocket connection (Mercury protocol) | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-device/` | Device registration (WDM) | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-conversation/` | Conversation service, activity threading | Partial | [`packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md`](../packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md) |
| `packages/@webex/internal-plugin-metrics/` | Telemetry and operational metrics | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-locus/` | Locus meeting-signaling protocol | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-llm/` | LLM/AI assistant integration | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-feature/` | Feature flag evaluation | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-presence/` | Presence internal protocol | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-calendar/` | Calendar service integration | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-dss/` | Directory Search Service | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-encryption/` | Internal KMS protocol helpers | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-wdm/` | Webex Device Manager (internal) | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-scheduler/` | Scheduled task management | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-search/` | Search service integration | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-support/` | Support/diagnostics | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-user/` | User identity plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-voicea/` | Voicea AI voice plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-ai-assistant/` | AI assistant plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-avatar/` | Avatar service plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-board/` | Board/whiteboard plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-ediscovery/` | eDiscovery plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-flag/` | Flag service plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-lyra/` | Lyra device plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-task/` | Task service plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/internal-plugin-team/` | Team service plugin | Untracked | No canonical spec (Untracked) |
| `packages/@webex/media-helpers/` | WebRTC media utilities | Untracked | No canonical spec (Untracked) |
| `packages/@webex/helper-html/` | HTML sanitization helpers | Untracked | No canonical spec (Untracked) |
| `packages/@webex/helper-image/` | Image processing helpers | Untracked | No canonical spec (Untracked) |

## Task Routing

| Task | Docs to load |
|---|---|
| New developer setup | `AGENTS.md` + `ai-docs/GETTING_STARTED.md` |
| Architecture overview | `AGENTS.md` + `ai-docs/ARCHITECTURE.md` |
| OAuth / auth changes | `AGENTS.md` + `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` + browser or node spec |
| Calling / meeting creation | `AGENTS.md` + `packages/calling/ai-docs/calling-spec.md` |
| Contact Center agent flows | `AGENTS.md` + `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| Encryption / KMS | `AGENTS.md` + `packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md` |
| Conversation threading | `AGENTS.md` + `packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md` |
| Coding conventions / PR review | `ai-docs/RULES.md` |
| Security review | `ai-docs/SECURITY.md` |
| Dependency audit | `docs/dependency/audit-process.md` + `docs/dependency/unmaintained-catalog.md` |
| `webex` package init / entry points | `AGENTS.md` + `packages/webex/ai-docs/webex-spec.md` |
| `webex-node` package (Node.js SDK) | `AGENTS.md` + `packages/webex-node/ai-docs/webex-node-spec.md` |
| Plugin system / HTTP pipeline / credentials / storage / services | `AGENTS.md` + `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| Any untracked module | `AGENTS.md` + `ai-docs/ARCHITECTURE.md` + read `packages/@webex/<module>/src/index.js` directly |

## Spec Registry

| Doc | Path |
|---|---|
| Architecture | [`ai-docs/ARCHITECTURE.md`](ARCHITECTURE.md) |
| Getting Started | [`ai-docs/GETTING_STARTED.md`](GETTING_STARTED.md) |
| Rules / Conventions | [`ai-docs/RULES.md`](RULES.md) |
| Security | [`ai-docs/SECURITY.md`](SECURITY.md) |
| Contracts | [`ai-docs/CONTRACTS.md`](CONTRACTS.md) |
| Glossary | [`ai-docs/GLOSSARY.md`](GLOSSARY.md) |
| ADRs | [`ai-docs/adr/`](adr/) — scaffold only; no ADRs recorded yet |

## incident history

> Record post-mortems, spec-driven incidents, or notable API breakages here so future contributors understand what went wrong and how the spec was updated in response.

| Date | Incident | Impact | Spec change |
|---|---|---|---|
| [NEEDS HUMAN INPUT] | — | — | — |

No incidents have been recorded at bootstrap time. Add entries here when a spec error causes a production incident or a significant developer-hours regression.

## Phase-Based Loading Protocol

- **Orient:** Load `AGENTS.md` + `SPEC_INDEX.md`
- **Specify:** Load the module spec for the relevant module from the table above
- **Build:** Load the module spec + `ARCHITECTURE.md` for cross-module dependency context
- **Verify:** Load the module spec + `RULES.md` + `SECURITY.md` as applicable
