<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: architecture@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# ARCHITECTURE — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md). This is the system architecture; per-module detail lives in each manifest-routed module spec, source-local as `<module-path>/ai-docs/<module-name>-spec.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; this loads on demand, not upfront.

## Design Overview
webex-js-sdk is a Yarn-workspaces monorepo that ships Cisco's public Webex JavaScript SDK as a family
of independently publishable npm packages. The unified `webex` package is the consumer entry point; it
composes public `@webex/plugin-*` plugins on top of the `@webex/webex-core` foundation. Core provides a
layered architecture — a public SDK surface, a plugin layer, the core services layer (HTTP pipeline,
authentication, storage, configuration), and a foundation layer (AmpersandState, event emitter, HTTP
core). This layering keeps plugin boundaries clear, HTTP handling robust, and configuration flexible.

The repository is multi-target: the same packages run in Node.js and in the browser, with per-file
`.shim.js` browser overrides declared in `package.json` `browser` maps and a UMD bundle published for
CDN use. Packages depend on each other through the `workspace:*` protocol, and each package owns its own
build/test scripts rather than sharing a single build.

Only `@webex/webex-core` has a canonical SDD spec in this assess-only onboarding pass; it is the module
every other plugin builds on and the one the routed source document (`webex-plugin-architecture.md`)
described. The rest of the workspace map is recorded as `Untracked` pending a human-confirmed module set.

## Component Inventory & Responsibilities
| Component | Responsibility (one line) | Docs |
|---|---|---|
| `packages/@webex/webex-core/` | Plugin registration, HTTP interceptor pipeline, credentials/services, storage abstraction, config, and events | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| `packages/webex/` | Unified consumer SDK that requires the public plugin set on top of core | `[NEEDS HUMAN INPUT]` (Untracked) |
| `packages/@webex/plugin-*/` | Public feature plugins (people, rooms, messages, meetings, memberships, webhooks, …) | `[NEEDS HUMAN INPUT]` (Untracked) |
| `packages/@webex/internal-plugin-*/` | Internal plugins (device, mercury, locus, metrics, encryption, …) | `[NEEDS HUMAN INPUT]` (Untracked) |
| `packages/@webex/storage-adapter-*/` | Storage backends (local-storage, session-storage, local-forage) | `[NEEDS HUMAN INPUT]` (Untracked) |

## Component Interaction
```mermaid
graph TD
  App[Consumer app] -->|import Webex from 'webex'| Webex[webex unified]
  Webex -->|Webex.init + requires| Core[@webex/webex-core: WebexCore]
  Pub[public @webex/plugin-*] -->|registerPlugin| Core
  Int[internal @webex/internal-plugin-*] -->|registerInternalPlugin| InternalCore[WebexInternalCore]
  Core --> InternalCore
  Core -->|request pipeline| Svc[Webex platform services]
  Core --> Store[storage adapters]
```
Narrative: applications enter through the unified `webex` package, whose `Webex.init` extends
`WebexCore` and auto-requires the public plugins. Each plugin registers onto core via `registerPlugin`
(or `registerInternalPlugin` for internal plugins), which adds it to the AmpersandState `_children`
tree, merges its config, and appends its interceptors to the shared HTTP pipeline. All network traffic
flows out through that pipeline to Webex services; storage access flows through core's storage
abstraction to the configured adapter.

## Execution & Flow
Init & Call Flow (grounded in `packages/@webex/webex-core/src/webex-core.js` and `src/index.js`):
`Webex.init({credentials})` → merge default + user config → `WebexCore` constructor normalizes
credentials → `AmpersandState` init → `WebexCore.initialize` sets up config, loaded/ready events, the
interceptor chain, the request function, and a session id → registered plugins are instantiated and
their `initialize()` runs → system is `ready` when all plugins are ready. A subsequent
`webex.<plugin>.<method>()` delegates to `this.request()`, runs the full interceptor pipeline, and
returns the processed response.

## Dependencies
| Dependency | Type (internal / external / peer) | How used | Failure / version handling |
|---|---|---|---|
| `@webex/http-core` | internal | HTTP request primitives, interceptor base, status handling | `workspace:*` version pin |
| `@webex/common` | internal | event proxying, retry, transfer helpers | `workspace:*` version pin |
| `ampersand-state` | external | base state/model management for core and plugins | `^5.0.3` (`packages/@webex/webex-core/package.json`) |
| Webex platform services | external | service discovery, auth `/token`, resource APIs | redirect/replay caps in `config.js` (`maxAppLevelRedirects`, `maxAuthenticationReplays`); fail-closed auth |
| npm registry `engci-maven-master.cisco.com` | external | publish target for `@webex/*` packages | `publishConfig.registry` |

<!-- repo.owns_datastore = false → Data & Schema section dropped -->

### State Model
<!-- repo.holds_client_state = true -->
Core and each plugin are AmpersandState models. `WebexCore` carries session properties (`config`,
`loaded`, `request`, `sessionId`) and derived state (`boundedStorage`, `unboundedStorage`, `ready`).
Child plugins hold their own state and bubble `change:*` events to the parent with a namespace prefix
(e.g. `change:people`); `ready` recomputes as child readiness changes. This is in-memory client state,
not a server datastore.

## Cross-Cutting Concerns
- **Security:** Authentication is token-based. Credentials are normalized in the `WebexCore`
  constructor and applied by the `AuthInterceptor`, which adds authorization headers, detects 401s, and
  can refresh + replay (bounded by `maxAuthenticationReplays`). Never log or commit tokens. See
  `SECURITY.md`.
- **Observability:** `@webex/plugin-logger` plus request/response/timing interceptors
  (`RequestTimingInterceptor`, `NetworkTimingInterceptor`, `ResponseLoggerInterceptor`) provide logging
  and timing; a `WebexTrackingIdInterceptor` propagates a tracking id per request.

## Non-Functional Posture
Footprint & Compatibility — packages are published to npm with semver; the unified `webex` bundle also
ships as a UMD/CDN artifact. Multi-target (Node + browser) support is maintained through `.shim.js`
overrides. Compatibility for public surfaces follows the Compatibility & Deprecation Policy in
`CONTRACTS.md`.

<!-- Include if: components/services call each other or exchange events [condition-id: repo.components_interact] -->
## Dependency / Interaction Topology
```mermaid
graph LR
  webex -->|requires| core[@webex/webex-core]
  plugins[@webex/plugin-*] -->|registerPlugin call| core
  internal[@webex/internal-plugin-*] -->|registerInternalPlugin call| core
  core -->|change:* events| plugins
```
| From | To | Kind (call / event) | Purpose |
|---|---|---|---|
| `webex` | `@webex/webex-core` | call | init/extend the core, run the request pipeline |
| `@webex/plugin-*` | `@webex/webex-core` | call | register plugin, config, interceptors |
| `@webex/webex-core` | plugins | event | propagate namespaced `change:*` / `ready` events |

<!-- repo.domain_data_across_components = null → Object / Data Ownership emitted as scaffold -->
## Object / Data Ownership
[NEEDS HUMAN INPUT] — monorepo-wide domain-data ownership across all packages was not established in
this assess-only pass. For `@webex/webex-core`, the owned in-memory objects (config, session, credential
state) are documented in its module spec.
| Domain object | System-of-record (owning component) | Read by |
|---|---|---|
| `[NEEDS HUMAN INPUT]` | `[NEEDS HUMAN INPUT]` | `[NEEDS HUMAN INPUT]` |

<!-- Include if: the repo caches data [condition-id: repo.caches_data] -->
## Caching Catalog
| Cache | Backend | What it holds | TTL | Invalidation trigger |
|---|---|---|---|---|
| bounded storage | configured storage adapter (default `MemoryStoreAdapter`) | size-limited, frequently accessed data | adapter/config-defined | explicit `del`/`clear` |
| unbounded storage | configured storage adapter | archival data with no size limit | n/a | explicit `del`/`clear` |
| host map | in-memory (`HostMapInterceptor`) | service host resolution | request lifecycle | service discovery refresh |

<!-- Include if: the repo has a logging/metrics/audit convention worth standardizing [condition-id: repo.observability_convention] -->
## Observability Patterns
- **Logging:** `@webex/plugin-logger` provides a per-plugin logger; `RequestLoggerInterceptor` /
  `ResponseLoggerInterceptor` are enabled via `ENABLE_NETWORK_LOGGING` env flags. Never log tokens.
- **Metrics:** timing interceptors record request/network durations; a metrics plugin receives
  measurements via `WebexCore.measure()`.
- **Audit:** `[NEEDS HUMAN INPUT]` — no repo-wide audit convention was confirmed.

<!-- repo.deploys_to_infra = false → Infrastructure Matrix dropped -->

<!-- Include if: the repo inherits a shared/base library stack every module uses [condition-id: repo.shared_base_libs] -->
## Shared / Base Libraries
| Library | What every module inherits from it | Version floor |
|---|---|---|
| `@webex/webex-core` | plugin base classes, request pipeline, storage, config, events | `workspace:*` |
| `@webex/common` | shared event/retry/transfer utilities | `workspace:*` |
| `@webex/http-core` | HTTP primitives + interceptor base | `workspace:*` |
| `ampersand-state` | state/model base | `^5.0.3` |

<!-- Include if: the repo is a monorepo [condition-id: repo.is_monorepo] -->
## Package Map & Inter-Package Dependencies
- Workspace tooling: Yarn 3.4.1 workspaces; globs `packages/@webex/*`, `packages/webex`,
  `packages/webex-node`, `packages/calling`, `packages/byods`, `packages/config/*`, `packages/legacy/*`,
  `packages/tools/*` (`package.json`).
- Package → responsibility (abbreviated): `webex` (public unified SDK, visibility: public) requires
  `@webex/webex-core` (foundation, public) and the public `@webex/plugin-*` set; internal plugins
  (`@webex/internal-plugin-*`, internal) also register onto core; `@webex/storage-adapter-*` (public)
  provide storage backends; `@webex/*-tools` and `packages/config/*` (internal) are build/config only.
- Inter-package deps use the `workspace:*`/`workspace:^` protocol; release/version-sync is managed by
  `@webex/package-tools` and `standard-version` (`package.json` scripts).
- A full package-by-package table is [NEEDS HUMAN INPUT] pending the confirmed module set.

<!-- Include if: the repo targets multiple platforms [condition-id: repo.multi_platform] -->
## Platform Matrix
| Platform | Shared core vs per-platform | Entry / build | Notes |
|---|---|---|---|
| Node.js | shared package sources | `dist/index.js` (`main`), `webex-legacy-tools build` | `>=18` engines |
| Browser | shared sources + `.shim.js` overrides | `browser` map in `package.json`; UMD bundle via `build:script` | CDN via unpkg/jsdelivr |

<!-- Include if: the repo is published/consumed as a package [condition-id: repo.published_package] -->
## Release & Versioning
- Publish target: npm (internal `webex-release-npm` registry, host `engci-maven-master.cisco.com`).
- Semver via `standard-version`; per-package `deploy:npm`. Consumer version-sync via `@webex/package-tools`.
- Deprecation/changelog: `changelog:generate` (`standard-changelog`); detailed policy [NEEDS HUMAN INPUT].

<!-- repo.embedded_in_host = false → Host Integration & Theming dropped -->
<!-- repo.cross_repo_deps_material = false → Cross-Repo Dependency Graph dropped -->

<!-- Include if: the security architecture warrants its own view [condition-id: repo.security_arch_warranted] -->
## Security Architecture
Trust boundary is the network edge between the SDK client and Webex platform services. Identity is
established via OAuth tokens normalized in the `WebexCore` constructor and carried by the
`AuthInterceptor`; token refresh + request replay are bounded by `config.js` limits. Transport security
is delegated to the platform HTTPS endpoints. Detailed posture and gaps are in `SECURITY.md`.

---
→ Per-module orientation and detailed design live in each manifest-routed module spec, source-local as `<module-path>/ai-docs/<module-name>-spec.md`. Routing: `SPEC_INDEX.md`.

## Architecture Reference Links
| Reference | Location | When to read |
|---|---|---|
| Architecture decisions | `adr/` | To understand why major design choices were made (none recorded yet) |
| Repo patterns | `patterns/` | To follow established implementation conventions (not yet extracted) |
| Enforceable rules | `RULES.md` | To understand constraints every architecture-affecting change must obey |
