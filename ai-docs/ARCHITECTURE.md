# ARCHITECTURE — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md). This is the system architecture; per-module detail lives in each manifest-routed module spec at `<module-path>/ai-docs/<module-name>-spec.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; this loads on demand, not upfront.

> **SDD status: assess-only draft (non-authoritative).** Repo-level content here was migrated **by
> meaning** from `webex-plugin-architecture.md` (routed source, `migrate-existing`). Where a statement
> comes from that source it reflects the source; code remains the source of truth. Each routed source
> doc has been migrated **by meaning** into its owning module's canonical spec
> (`<module-path>/ai-docs/<module-name>-spec.md`); full public-surface coverage and independent
> `source-fidelity-review` are a rigorous-mode step.

## Design Overview

The Webex JS SDK is a plugin-oriented monorepo. `@webex/webex-core` is the foundational package: it
provides plugin registration, a composable HTTP request pipeline, authentication/credential handling,
storage management, configuration merging, and an event system that every other package builds on.
Public plugins (meetings, people, rooms, messages, authorization, encryption, presence, …) and
internal plugins (device, mercury, locus, conversation, metrics, …) attach to the core through a
uniform `WebexPlugin` base class. The unified `webex` / `webex-node` bundles compose these plugins into
a ready-to-use SDK, while modular consumers can import individual `@webex/plugin-*` packages.

The design favors clear separation of concerns: a layered stack (SDK → plugins → core → foundation),
plugin boundaries with namespaced config and storage, an interceptor pipeline that centralizes
cross-cutting request behavior (auth, service resolution, payload transform/encryption, timing,
retry), and an event system where child (plugin) events bubble to the root instance. Standalone
capability SDKs (`calling`, `byods`, `contact-center`) live in the same workspace with their own
build/test setups.

*(Source: `webex-plugin-architecture.md`, "Overview" and "Architecture Overview".)*

## Component Inventory & Responsibilities

| Component | Responsibility (one line) | Docs |
|---|---|---|
| `packages/@webex/webex-core/` | Plugin registry, HTTP pipeline, auth/credentials, storage, config, events | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| `packages/@webex/http-core/` | Low-level HTTP request execution + primitives | `packages/@webex/http-core/ai-docs/http-core-spec.md` |
| `packages/@webex/common*`, `helper-*`, `media-helpers`, `webrtc` | Shared utilities, decorators, timers, media/WebRTC helpers | per-module spec |
| `packages/@webex/plugin-*/` | Public capability plugins (meetings, people, rooms, messages, authorization*, encryption, presence, memberships, teams, webhooks, …) | per-module spec |
| `packages/@webex/internal-plugin-*/` | Internal plugins (device, mercury, locus, conversation, metrics, dss, feature, flag, user, wdm, …) | per-module spec |
| `packages/@webex/storage-adapter-*/` | Bounded/unbounded storage adapters (local-storage, session-storage, local-forage, spec) | per-module spec |
| `packages/@webex/test-helper-*`, `test-*`, `xunit-with-logs` | Test infrastructure / helpers | per-module spec |
| `packages/webex`, `packages/webex-node` | Unified SDK entry bundles that compose plugins | per-module spec |
| `packages/calling`, `packages/byods`, `packages/@webex/contact-center` | Standalone capability SDKs | per-module spec |

→ Full module registry with coverage state: [`SPEC_INDEX.md`](SPEC_INDEX.md) (mirrors `.sdd/manifest.json`).

## Component Interaction

```
Consumer → Webex.init(attrs)                        [webex/src/webex.js]
   → WebexCore(attrs)  (credential normalization, AmpersandState init)
     → WebexCore.initialize()  (config merge, event handlers, interceptor chain, request fn, sessionId)
       → mixinWebexCorePlugins / mixinWebexInternalCorePlugins → registerPlugin() per plugin
plugin.method() → this.request() → webex.request() → interceptor pipeline → http-core → response
child plugin `change`/`ready` events → bubble to root WebexCore (namespace-prefixed)
```

`Webex.init(attrs)` merges default config, sets `sdkType: 'webex'`, extends `WebexCore`, and requires
the public plugins. Plugins register via `registerPlugin(name, constructor, options)` where options
carry `proxies` (root-level method proxying), `interceptors`, `config`, `payloadTransformer`
predicates/transforms, and `onBeforeLogout` cleanup. Public plugins register on `WebexCore`; internal
plugins on `WebexInternalCore`.

*(Source: `webex-plugin-architecture.md`, "Webex Object Creation Process", "Plugin System Architecture".)*

## Execution & Flow

*Shape for this repo: **Init & Request Flow** (library SDK).*

**Init flow:** `Webex.init({credentials})` → default+user config merge → `WebexCore` constructor
(token normalization across formats, `bearerValidator()`) → AmpersandState init → `initialize()`
(config setup, ready/loaded handlers, interceptor chain build, request-function creation, sessionId)
→ plugins instantiated and each `initialize()` called → system `ready` when all plugins ready.

**HTTP request flow:** `webex.request(options)` →
1. Pre-interceptors: `ResponseLogger`, `RequestTiming`, `RequestEvent`, `WebexTrackingId`, `RateLimit`
2. Core interceptors: `Service`, `UserAgent`, `WebexUserAgent`, `Auth`, `PayloadTransformer`, `Redirect`
3. Post-interceptors: `HttpStatus`, `NetworkTiming`, `Embargo`, `RequestLogger`, `RateLimit`

`AuthInterceptor` adds authorization headers, detects 401s, decides on re-auth/token refresh, and
replays failed requests after refresh (bounded by `maxAuthenticationReplays`). `PayloadTransformer`
applies bidirectional transforms including encryption/decryption.

**Plugin method flow:** `webex.people.get('id')` → core resolves the `people` child → `People.get()`
→ `this.request()` → `webex.request()` → full pipeline → plugin processes response.

*(Source: `webex-plugin-architecture.md`, "HTTP Request Pipeline", "Detailed Code Flow Analysis".)*

## Dependencies

| Dependency | Type | How used | Failure / version handling |
|---|---|---|---|
| AmpersandState | external | Base state management for `WebexCore`/`WebexPlugin` | Peer of the SDK; state derived/session props |
| `@webex/http-core` | internal | Actual HTTP execution beneath the interceptor pipeline | Workspace dep, version-synced |
| `@webex/webex-core` | internal | Foundation every plugin extends | Workspace dep, version-synced |
| npmjs registry | external | Resolves all deps (`https://registry.npmjs.org`) | `.yarnrc.yml`; `checksumBehavior: update` |
| Webex platform services | external | Runtime target of SDK requests (discovery URLs in config) | Service discovery + `validateDomains`; redirects bounded |

## Cross-Cutting Concerns
- **Security:** Tokens normalized and validated (`bearerValidator`) and held by core credentials;
  `AuthInterceptor` injects auth and handles 401/refresh/replay; payload transformer performs
  encryption/decryption; `.env` is git-ignored; secrets are never logged.
- **Observability:** Request/response logging interceptors, `RequestTiming`/`NetworkTiming`, a
  `WebexTrackingId` per request, and a metrics plugin (`webex.measure()` / internal-plugin-metrics).

## Non-Functional Posture

*Shape for this repo: **Footprint & Compatibility** (published library).*

Published as npm packages (unified + modular). Node 18.x baseline; browser support via bundles and CDN
distributables. Semver governs public packages; changelog obligation on consumer-facing change. Bundle
size matters for the browser SDK (webpack build, min bundles under `docs/samples/`).

<!-- Conditional sections kept below reflect real repo signals. -->

## Dependency / Interaction Topology
```
webex / webex-node  ──requires──►  @webex/plugin-* (public) ──extends──► @webex/webex-core
                                     @webex/internal-plugin-* ──extends──► @webex/webex-core (internal core)
@webex/webex-core ──uses──► @webex/http-core, @webex/common*, storage-adapter-*
plugins ──emit──► change/ready events ──bubble──► root WebexCore
```
| From | To | Kind | Purpose |
|---|---|---|---|
| `webex` bundle | `@webex/plugin-*`, several `internal-plugin-*` | call (require/compose) | Assemble the unified SDK |
| any plugin | `@webex/webex-core` | call (extends `WebexPlugin`) | Core services (request, storage, config) |
| any plugin | root `WebexCore` | event (`change:*`, `ready`) | State/readiness propagation |

*(Inter-package edges are recorded per module in `.sdd/manifest.json` `modules[].internal_dependencies`.)*

## State Model
`WebexCore` and each plugin are AmpersandState models with session/derived properties (`config`,
`loaded`, `request`, `sessionId`, `ready`, `boundedStorage`, `unboundedStorage`). `ready` aggregates
child-plugin readiness; `loaded` signals initial storage hydration.

## Package Map & Inter-Package Dependencies
- Workspace tooling: **yarn 3.4.1** (`packageManager`), globs: `packages/@webex/*`, `packages/webex`,
  `packages/webex-node`, `packages/calling`, `packages/byods`, `packages/byods-demo-server`,
  `packages/config/*`, `packages/legacy/*`, `packages/tools/*`.
- Package → responsibility + visibility: see the Component Inventory above and `SPEC_INDEX.md`. Public
  = published `@webex/plugin-*` / `webex`; internal = `@webex/internal-plugin-*`, test helpers, tooling.
- Inter-package graph + version-sync: workspace-internal deps recorded per module in the manifest;
  release via `standard-version` (`yarn release`), semver-synced across published packages.

## Release & Versioning
Published to npmjs (unified `webex`/`webex-node` + modular `@webex/*`). Semver; breaking change → major
bump; consumer-facing changelog via `standard-version`. CDN distributables built by the samples/build
pipeline.

---
→ Per-module orientation and detailed design live in each manifest-routed module spec at
`<module-path>/ai-docs/<module-name>-spec.md` (pending rigorous-mode generation). Routing:
[`SPEC_INDEX.md`](SPEC_INDEX.md).

## Architecture Reference Links
| Reference | Location | When to read |
|---|---|---|
| Enforceable rules | `ai-docs/RULES.md` | Constraints every architecture-affecting change must obey |
| Security posture | `ai-docs/SECURITY.md` | Trust boundaries, token/credential handling |
| Public surface / contracts | `ai-docs/CONTRACTS.md` | Public API index + links to generated API reference |
| Routed source architecture | `webex-plugin-architecture.md` | Original core technical write-up (source-material) |

## WS6 References
No WS6 (workstream-6 / platform / shared-service / enterprise-architecture) specification is tracked
in this repository at onboarding. This is a client-side SDK component repo; the authoritative platform
contracts it targets are the live Webex backend services reached through service discovery, not a
committed WS6 document.

| WS6 artifact | Relevance to this repo | Link |
|---|---|---|
| None tracked | Add the platform/service-architecture spec here if/when this repo is linked to one | `[NEEDS HUMAN INPUT]` |
