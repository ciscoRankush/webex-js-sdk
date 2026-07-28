<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
  source_material: webex-plugin-architecture.md (migrate-existing), UPGRADING.md (context)
-->

# ARCHITECTURE.md — webex-js-sdk

> Repo-level architecture for the Cisco Webex JavaScript SDK monorepo.
> Per-module deep-dives: see [`SPEC_INDEX.md`](SPEC_INDEX.md).

## Design Overview

The Webex JS SDK is a **Yarn 3 workspace monorepo** of ~74 packages organized as a plugin-based architecture. The foundational package `@webex/webex-core` provides a plugin host, HTTP pipeline, credential management, storage, and an event system. All SDK capabilities are registered as plugins (public or internal) on top of this core.

Three independently publishable SDK surfaces exist:
- **`webex`** — the primary SDK package for browser + Node (aggregates all plugins)
- **`@webex/calling`** — standalone Calling SDK (TypeScript, XState FSM, independent release cadence)
- **`@webex/contact-center`** — standalone Contact Center SDK (TypeScript, depends on `@webex/calling`)

**Source material:** `webex-plugin-architecture.md`

## Component Inventory

| Component | Path | Responsibility |
|---|---|---|
| `webex` | `packages/webex/` | Primary SDK npm entry point; aggregates all plugins for browser + Node |
| `webex-node` | `packages/webex-node/` | Node.js-specific SDK entry (server-side defaults) |
| `@webex/webex-core` | `packages/@webex/webex-core/` | Plugin host, HTTP pipeline & interceptors, credentials, storage, config, event bus |
| `@webex/http-core` | `packages/@webex/http-core/` | Low-level HTTP request/response, interceptors, browser/Node shim |
| `@webex/common` | `packages/@webex/common/` | Shared utilities across plugins |
| `@webex/common-evented` | `packages/@webex/common-evented/` | Event mixin utilities |
| `@webex/common-timers` | `packages/@webex/common-timers/` | Timer abstraction utilities |
| `@webex/calling` | `packages/calling/` | Calling SDK: CallingClient, CallHistory, CallSettings, Voicemail, Contacts, XState FSM |
| `@webex/contact-center` | `packages/@webex/contact-center/` | Contact Center agent lifecycle, task management, WebRTC calling |
| `@webex/byods` | `packages/byods/` | Bring Your Own Data Store — custom data persistence SDK |
| `@webex/plugin-meetings` | `packages/@webex/plugin-meetings/` | Full Meetings plugin: WebRTC, ROAP, Locus, media, breakouts, transcription, reactions |
| `@webex/plugin-authorization` | `packages/@webex/plugin-authorization/` | OAuth flow orchestration, environment detection |
| `@webex/plugin-authorization-browser` | `packages/@webex/plugin-authorization-browser/` | Browser OAuth: implicit grant, authcode grant, CSRF, URL parsing |
| `@webex/plugin-authorization-node` | `packages/@webex/plugin-authorization-node/` | Node.js OAuth: authcode, JWT auth, client credentials |
| `@webex/plugin-encryption` | `packages/@webex/plugin-encryption/` | E2E encryption via KMS, file decrypt (`cypher.downloadAndDecryptFile`) |
| `@webex/plugin-messages` | `packages/@webex/plugin-messages/` | Messaging API (create/list/delete messages) |
| `@webex/plugin-rooms` | `packages/@webex/plugin-rooms/` | Spaces/Rooms management API |
| `@webex/plugin-memberships` | `packages/@webex/plugin-memberships/` | Room membership API |
| `@webex/plugin-people` | `packages/@webex/plugin-people/` | People/identity API |
| `@webex/plugin-presence` | `packages/@webex/plugin-presence/` | Presence status API (public) |
| `@webex/plugin-teams` | `packages/@webex/plugin-teams/` | Teams API |
| `@webex/plugin-team-memberships` | `packages/@webex/plugin-team-memberships/` | Team membership API |
| `@webex/plugin-webhooks` | `packages/@webex/plugin-webhooks/` | Webhooks registration and management |
| `@webex/plugin-attachment-actions` | `packages/@webex/plugin-attachment-actions/` | Card/attachment actions API |
| `@webex/plugin-device-manager` | `packages/@webex/plugin-device-manager/` | Device pairing and management |
| `@webex/plugin-logger` | `packages/@webex/plugin-logger/` | SDK-wide logging plugin |
| `@webex/internal-plugin-mercury` | `packages/@webex/internal-plugin-mercury/` | WebSocket connection (Mercury protocol): auth, reconnect, event dispatch |
| `@webex/internal-plugin-device` | `packages/@webex/internal-plugin-device/` | Device registration with Webex Device Manager (WDM) |
| `@webex/internal-plugin-conversation` | `packages/@webex/internal-plugin-conversation/` | Conversation service: activity threading, message ordering |
| `@webex/internal-plugin-metrics` | `packages/@webex/internal-plugin-metrics/` | Telemetry, operational metrics |
| `@webex/internal-plugin-locus` | `packages/@webex/internal-plugin-locus/` | Locus meeting-signaling protocol |
| `@webex/internal-plugin-llm` | `packages/@webex/internal-plugin-llm/` | LLM/AI assistant integration |
| `@webex/internal-plugin-feature` | `packages/@webex/internal-plugin-feature/` | Feature flag evaluation |
| `@webex/internal-plugin-presence` | `packages/@webex/internal-plugin-presence/` | Presence (internal protocol) |
| `@webex/internal-plugin-calendar` | `packages/@webex/internal-plugin-calendar/` | Calendar service integration |
| `@webex/internal-plugin-dss` | `packages/@webex/internal-plugin-dss/` | Directory Search Service |
| `@webex/internal-plugin-encryption` | `packages/@webex/internal-plugin-encryption/` | Internal encryption protocol helpers |
| `@webex/internal-plugin-wdm` | `packages/@webex/internal-plugin-wdm/` | Webex Device Manager (internal) |
| `@webex/internal-plugin-scheduler` | `packages/@webex/internal-plugin-scheduler/` | Scheduled task management |
| `@webex/internal-plugin-search` | `packages/@webex/internal-plugin-search/` | Search service integration |
| `@webex/internal-plugin-support` | `packages/@webex/internal-plugin-support/` | Support/diagnostics |
| `@webex/internal-plugin-user` | `packages/@webex/internal-plugin-user/` | User identity internal plugin |
| `@webex/internal-plugin-voicea` | `packages/@webex/internal-plugin-voicea/` | Voicea (AI voice) plugin |
| `@webex/media-helpers` | `packages/@webex/media-helpers/` | WebRTC media utilities |
| `@webex/helper-html` | `packages/@webex/helper-html/` | HTML sanitization helpers |
| `@webex/helper-image` | `packages/@webex/helper-image/` | Image processing helpers |
| `@webex/jsdoctrinetest` | `packages/@webex/jsdoctrinetest/` | JSDoc test runner utility |

## Layered Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Consumer Application                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│              SDK Entry Points                                    │
│  webex (browser + Node)  |  webex-node  |  @webex/calling       │
│  @webex/contact-center   |  @webex/byods                        │
└─────────────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┴────────────────────┐
          ▼                                         ▼
┌──────────────────────┐             ┌──────────────────────────┐
│   Public Plugin Layer│             │  Standalone SDK Layer     │
│  plugin-meetings     │             │  @webex/calling           │
│  plugin-authorization│             │  (XState FSM, TypeScript) │
│  plugin-encryption   │             │  @webex/contact-center    │
│  plugin-messages     │             │  (agent lifecycle)        │
│  plugin-rooms, etc.  │             │  @webex/byods             │
└──────────────────────┘             └──────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────────┐
│                 @webex/webex-core                                │
│  Plugin Registry  │  HTTP Pipeline  │  Credentials/Auth         │
│  Storage          │  Config System  │  Event Bus (AmpersandJS)  │
│  Interceptor Chain│  Transform API  │  Token Refresh            │
└─────────────────────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────────┐
│  Internal Plugin Layer (registered via registerInternalPlugin)  │
│  internal-plugin-mercury (WebSocket)  │  internal-plugin-device │
│  internal-plugin-conversation         │  internal-plugin-locus  │
│  internal-plugin-metrics              │  internal-plugin-feature│
│  internal-plugin-encryption           │  internal-plugin-llm    │
└─────────────────────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────────┐
│                 @webex/http-core                                 │
│   Request / Response  │  Interceptors  │  Browser/Node shims    │
└─────────────────────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────────┐
│                  Foundation                                      │
│  AmpersandState (state mgmt)  │  EventEmitter  │  @webex/common │
└─────────────────────────────────────────────────────────────────┘
```

## WebexCore Initialization Flow

*Source material: `webex-plugin-architecture.md`*

1. Consumer calls `Webex.init({config})` or `require('webex/env')`
2. `WebexCore` constructor normalizes credentials (merges `access_token`, `authorization` fields)
3. `initialize()` sets up the config system, attaches interceptors to the HTTP pipeline, and registers event listeners
4. Each registered plugin is initialized in dependency order via `registerPlugin` / `registerInternalPlugin`
5. `internal-plugin-device` registers the device with Webex Device Manager
6. `internal-plugin-mercury` opens a WebSocket connection for push events
7. Authorization plugins (`plugin-authorization-browser` or `plugin-authorization-node`) detect environment and trigger the appropriate OAuth flow
8. `webex.once('ready', cb)` fires after all plugins are initialized and the device is registered

## HTTP Request Pipeline

*Source material: `webex-plugin-architecture.md`*

Interceptor chain (applied in order):
1. `RequestEventInterceptor` — emits request lifecycle events
2. `AuthInterceptor` — injects access token into Authorization header; triggers refresh on 401
3. `KmsDryRun` — skips KMS operations in dry-run mode
4. `PayloadTransformerInterceptor` — applies `transform()`/`applyNamedTransform()` to request/response body
5. `ConversationInterceptor` — conversation-specific transforms
6. `NetworkTimingInterceptor` — records network latency
7. `ResponseLogInterceptor` — logs responses

## Plugin Registration

Evidence: `packages/@webex/internal-plugin-mercury/src/index.js`, `packages/@webex/webex-core/src/index.js`

- `registerPlugin(name, Plugin, {config, onBeforeLogout})` — registers a public-facing plugin
- `registerInternalPlugin(name, Plugin, {config, onBeforeLogout})` — registers an internal plugin; not exposed on the public Webex instance surface
- All plugins extend `AmpersandState` and participate in the shared event bus and config system

## Storage System

Three storage adapter packages provide pluggable storage:
- `@webex/storage-adapter-local-storage` — browser `localStorage`
- `@webex/storage-adapter-session-storage` — browser `sessionStorage`
- `@webex/storage-adapter-local-forage` — cross-platform (IndexedDB/WebSQL/localStorage)

## Configuration Management

- Plugins declare a `config` object with defaults
- `WebexCore.setConfig(delta)` merges configuration at runtime
- `credentials-config.js` in `webex-core` defines credential-related defaults

## Event System

- Built on AmpersandJS `ampersand-events`
- All plugins inherit the shared event bus from `WebexCore`
- Key lifecycle events: `ready`, `change:credentials`, `change:config`

## Cross-Cutting Concerns

### Security
- OAuth 2.0 via `plugin-authorization` family; browser implicit grant or authcode grant; Node.js authcode or JWT
- E2E encryption via KMS (`plugin-encryption`, `internal-plugin-encryption`)
- CSRF protection in browser OAuth flow
- Access tokens never stored in source; `credentials-config.js` defines storage policy

### Observability
- `internal-plugin-metrics` provides telemetry
- `plugin-logger` provides structured SDK logging
- `@webex/calling` has an independent `Logger` subsystem under `packages/calling/src/Logger/`
- `NetworkTimingInterceptor` records per-request latency in the HTTP pipeline

## Monorepo Package Map

<!-- Include if: monorepo topology -->

```
packages/
  webex/                 — main SDK entry (dist/index.js, dist/calling.js, dist/meetings.js)
  webex-node/            — Node.js SDK entry
  calling/               — @webex/calling (TypeScript, tsc build, XState)
  byods/                 — @webex/byods (TypeScript, tsc)
  byods-demo-server/     — demo server
  @webex/
    webex-core/          — plugin host & core infrastructure
    http-core/           — HTTP core
    common/              — shared utilities
    common-evented/      — event utilities
    common-timers/       — timer utilities
    contact-center/      — Contact Center SDK (TypeScript, depends on @webex/calling)
    helper-html/         — HTML utilities
    helper-image/        — image utilities
    media-helpers/       — WebRTC media utilities
    jsdoctrinetest/      — JSDoc test runner
    plugin-*/            — public plugins (authorization, meetings, encryption, messages, rooms, etc.)
    internal-plugin-*/   — internal plugins (mercury, device, conversation, metrics, locus, etc.)
  config/
    eslint/              — shared ESLint profiles (javascript.modern, typescript.modern, jest.modern)
    jest/                — shared Jest config
    typescript/          — shared tsconfig
    api-extractor/       — API Extractor config
  legacy/                — legacy build tools (babel-config, eslint-config, jest-config, env-config)
  tools/                 — @webex/cli-tools, @webex/package-tools
```

## Build and Release

- **Topological workspace builds:** `yarn workspaces foreach --topological` ensures dependency order
- **Legacy packages** (`@webex/webex-core`, `plugin-*`, `internal-plugin-*`): `webex-legacy-tools build` (Babel)
- **Modern TypeScript packages** (`@webex/calling`, `@webex/byods`): plain `tsc` + declaration emit
- **Mixed TypeScript packages** (`plugin-meetings`, `plugin-encryption`): `webex-legacy-tools build` + `tsc --declaration`
- **Publish registry:** `http://engci-maven-master.cisco.com/artifactory/api/npm/webex-release-npm`
- **Release:** `standard-version` (conventional changelog)
- **CI/CD:** GitHub Actions (`deploy.yml`, `pull-request.yml`), AWS Amplify (`amplify.yml`) for docs

## component inventory & responsibilities

See the full table in [## Component Inventory](#component-inventory) above. Summary of primary responsibility domains:

| Layer | Key Components | Responsibility |
|---|---|---|
| **SDK Entry** | `webex`, `webex-node` | Aggregate all plugins; expose the top-level `Webex` constructor |
| **Core Infrastructure** | `@webex/webex-core`, `@webex/http-core` | Plugin host, HTTP pipeline, interceptors, credentials, storage, event bus |
| **Public Plugin Layer** | `plugin-meetings`, `plugin-authorization-*`, `plugin-encryption`, `plugin-messages`, `plugin-rooms`, etc. | Feature capabilities exposed on the public `webex.*` surface |
| **Internal Plugin Layer** | `internal-plugin-mercury`, `internal-plugin-device`, `internal-plugin-locus`, `internal-plugin-conversation`, etc. | Platform protocol adapters — not exposed on the public API surface |
| **Standalone SDKs** | `@webex/calling`, `@webex/contact-center`, `@webex/byods` | Independently-released SDKs with their own entry points and release cadence |
| **Foundation** | `@webex/common`, `@webex/common-evented`, `@webex/common-timers` | Shared utilities and mixins used across all layers |
| **Build / Tooling** | `@webex/legacy-tools`, `@webex/package-tools`, `@webex/cli-tools` | Build pipeline, workspace management, Babel and tsc compilation |

## component interaction

Interaction patterns between components:

- **Plugin → Core:** All plugins are registered on and initialized by `@webex/webex-core` via `registerPlugin` / `registerInternalPlugin`. Plugins receive the `WebexCore` instance and may access sibling plugins through it (e.g. `this.webex.internal.mercury`).
- **Plugin → HTTP pipeline:** Plugins issue requests via `this.webex.request()`, which flows through the ordered interceptor chain in `@webex/http-core` (Auth → Transform → Network timing → Logging).
- **Plugin → Storage:** Plugins access namespaced storage through `this.webex.boundedStorage.get/set` backed by a configured storage adapter.
- **Plugin → Event Bus:** All plugins share the AmpersandJS event bus on `WebexCore`. Key system events (`ready`, `change:credentials`, `change:config`) are emitted on the core instance.
- **Internal plugins → External services:** `internal-plugin-mercury` maintains a WebSocket to the Mercury push-event service; `internal-plugin-device` registers with WDM; `internal-plugin-locus` communicates with the Locus signaling service.
- **Standalone SDK → Core SDK:** `@webex/calling` connects to the core SDK instance via `SDKConnector`; `@webex/contact-center` depends on `@webex/calling` for WebRTC.
- **Consumer App → SDK:** Consumer creates a `Webex` instance with `Webex.init({config})` and awaits the `ready` event before making API calls.

## execution & flow

Primary execution paths:

**SDK Initialization (see WebexCore Initialization Flow above):**
`Webex.init()` → normalize credentials → attach interceptors → register plugins in dependency order → device registration (WDM) → Mercury WebSocket open → `ready` event

**OAuth Browser Flow:**
Consumer calls `webex.authorization.initiateLogin()` → SDK detects `clientType` → redirect to Webex ID Broker → callback with `code` or token in URL hash → SDK extracts and stores token → `ready` event

**HTTP Request:**
`webex.request(options)` → `RequestEventInterceptor` → `AuthInterceptor` (inject token, handle 401 refresh) → `PayloadTransformerInterceptor` → `NetworkTimingInterceptor` → network call → `ResponseLogInterceptor` → caller

**Calling (XState FSM):**
`CallingClient.makeCall(destination)` → XState FSM transitions `idle → calling → connected` → SIP signaling via Locus → WebRTC negotiation (ROAP/SDP) → media streams established

**Mercury Push Event:**
Mercury WebSocket receives message → `internal-plugin-mercury` dispatches event on the core event bus → subscribed plugins handle the event (e.g., `internal-plugin-locus` handles meeting state changes)

## dependencies

**Runtime npm dependencies (key):**

| Dependency | Used by | Purpose |
|---|---|---|
| `ampersand-state`, `ampersand-events` | `@webex/webex-core`, all legacy plugins | Plugin state management and event bus |
| `xstate` (v4) | `@webex/calling` | Call lifecycle state machine |
| `jose` | `@webex/byods`, auth plugins | JWT encoding/decoding |
| `lodash` | Multiple plugins | Utility functions |
| `webrtc-adapter` | `plugin-meetings`, `@webex/calling` | WebRTC cross-browser normalization |
| `node-fetch` | `@webex/byods`, http-core (Node) | HTTP client for Node.js |
| `uuid` | Multiple plugins | Unique ID generation |
| `jwt-decode` (3.1.2 pinned) | `plugin-meetings` | JWT decoding (pinned for Node 18 compat) |

**External service dependencies:** See [## External Service Dependencies] in `CONTRACTS.md`.

**Dev / build toolchain:** `@webex/legacy-tools` (Babel), `tsc` 4.9.5, Jest 29, Mocha, Karma, webpack 5, rollup, ESLint 8, commitlint, standard-version.

## non-functional posture

| Dimension | Posture |
|---|---|
| **Browser support** | Modern evergreen browsers (Chrome, Firefox, Edge, Safari); IE not supported |
| **Node.js support** | Node 18.x minimum |
| **Bundle size** | Pre-built UMD bundles in `packages/webex/umd/`; consumers may tree-shake via ES module entry points |
| **Latency** | SDK adds negligible overhead; network latency is recorded via `NetworkTimingInterceptor` and reported via `internal-plugin-metrics` |
| **Reliability** | Mercury WebSocket has built-in reconnect logic; `AuthInterceptor` handles token refresh on 401 automatically |
| **Security** | OAuth 2.0 only; E2E encryption via KMS; no credential logging; gitleaks on PRs |
| **Test coverage** | Global 85% floor (lines/functions/branches/statements); per-package exceptions in `jest.config.js` |
| **Observability** | `internal-plugin-metrics` for telemetry; `plugin-logger` for structured SDK logs; per-request network timing |
| **Release cadence** | Main SDK: conventional changelog + standard-version; `@webex/calling` and `@webex/contact-center` have independent cadences |
| **TypeScript support** | Declaration files emitted for all packages; `@webex/calling`, `@webex/byods`, `@webex/contact-center` are fully TypeScript |

## architecture reference links

- `webex-plugin-architecture.md` — canonical source for plugin architecture (migrated into this doc)
- `UPGRADING.md` — migration notes between major SDK versions
- `BUILD-ISSUES.md` — known build problems and resolutions
- `docs/dependency/audit-process.md` — dependency governance process
- `docs/labs/contact-center/README.md` — Contact Center SDK integration guide
- `packages/@webex/plugin-authorization-browser/BROWSER-OAUTH-FLOW-GUIDE.md` — browser OAuth guide
- `packages/@webex/plugin-authorization-node/NODE-OAUTH-FLOW-GUIDE.md` — Node.js OAuth guide
- `packages/@webex/plugin-encryption/developer-quickstart.md` — encryption quickstart
- `packages/@webex/internal-plugin-conversation/src/activity-threading.md` — activity threading design
- [`SPEC_INDEX.md`](SPEC_INDEX.md) — per-module spec router

## ws6 references

> [NEEDS HUMAN INPUT] — Record workspace-6 (WS6) or internal Cisco architecture review references here once available.

| Reference | Type | Description |
|---|---|---|
| [NEEDS HUMAN INPUT] | WS6 / internal ADR | Link to internal architecture review documents, Confluence pages, or Cisco design records that govern this SDK's platform integration |

## Known Constraints and Decisions

*Source material: `UPGRADING.md`, `BUILD-ISSUES.md`*

- `jwt-decode` pinned to `3.1.2` in `plugin-meetings` due to Node 18 compatibility issue (tracked in `package.json` `//` comment)
- `jsdom` pinned to `19.0.0` for browser-compatibility testing consistency
- `@webex/json-multistream` resolved to `2.4.3` via root `resolutions` to prevent version drift
- `publishConfig.registry` points to Cisco internal Artifactory — public npm publish requires separate configuration
- [NEEDS HUMAN INPUT] — Additional architectural decisions, ADRs, and long-term constraints should be recorded in `ai-docs/adr/`
