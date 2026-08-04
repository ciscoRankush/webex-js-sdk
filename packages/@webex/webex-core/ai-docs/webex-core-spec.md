# @webex/webex-core — SPEC

> Start here → root [`AGENTS.md`](../../../../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](../../../../ai-docs/SPEC_INDEX.md) · system [`ARCHITECTURE.md`](../../../../ai-docs/ARCHITECTURE.md). This is the module's canonical spec: orientation, requirements, design, flows, state, and tests.
> Context-efficiency: link to canonical docs — don't duplicate them. Load specs on demand per `SPEC_INDEX.md`.

## Metadata
| Field | Value |
|---|---|
| Module id | `@webex/webex-core` |
| Source path(s) | `packages/@webex/webex-core/` |
| Parent spec | — |
| Doc kind | Module spec |
| Coverage score | Pending coverage assessment |
| Generated from | `module-spec` @ SDLC template library `0.2.2` |
| generated_by / approved_by / updated_at | claude-cli / pending / 2026-08-04T00:00:00Z |
| Validation status | not-run |

## Evidence Rules
Every requirement below cites concrete source evidence using `file path`. Source evidence, test
evidence, and gaps are kept separate. This spec was migrated (policy `migrate-existing`) from the routed
source document, with claims re-grounded against current `src/`. Unresolved items are marked
`[NEEDS HUMAN INPUT]`. This is an assess-only draft; it is non-authoritative until validated by an
independent runtime.

## Source Material Register
| Source material | Scope | Decision | Detail location or disposition |
|---|---|---|---|
| Reviewed prior webex-core technical/architecture doc | architecture | used / verified | Layer model, plugin registration, interceptor pipeline, storage, config, and event content routed into Overview, Design Overview, Data Flow, Sequence Diagram(s), Public Surface, and Requirements; re-grounded against `src/`. |
| webex-core source tree | overview / API | verified | Exports and interceptor ordering verified against `src/index.js`, `src/webex-core.js`. |

## Overview
`@webex/webex-core` is the foundational infrastructure package of the Webex JavaScript SDK. It provides
the framework that all other Webex plugins build on: plugin registration, HTTP request handling with an
interceptor pipeline, authentication and credentials, storage management, configuration, and an
event-driven state model. It follows a layered architecture with clear separation of concerns — a
public SDK surface, a plugin layer, the core services layer, and a foundation layer built on
AmpersandState, an event emitter, and HTTP core.

A maintainer should start at `src/webex-core.js` (the `WebexCore` class and interceptor wiring),
`src/index.js` (the export barrel), `src/lib/webex-plugin.js` (the plugin base class), and
`src/config.js` (default configuration and limits). The package is consumed by the unified `webex`
package and by every `@webex/plugin-*` and `@webex/internal-plugin-*`, which register onto core.

## Purpose / Responsibility
Owns the SDK plugin/runtime foundation: registering plugins, building and running the HTTP interceptor
pipeline, normalizing credentials, exposing storage and configuration, and propagating lifecycle
events. It does NOT own feature behavior (people, rooms, meetings, etc.) or the concrete storage
backends — those live in feature plugins and storage-adapter packages.

## Stack
JavaScript (with TypeScript build support), Node `>=18`. Built with `@webex/legacy-tools` (Babel). Depends
on `ampersand-state` for the state model, `@webex/http-core` for HTTP primitives, and `@webex/common`
for event/retry helpers. Tests: Jest (unit), Mocha (integration), Karma (browser).

## Folder / Package Structure
```
packages/@webex/webex-core/src/
├── index.js                 # export barrel (public surface)
├── webex-core.js            # WebexCore class + interceptor wiring + upload/logout/measure
├── webex-internal-core.js   # WebexInternalCore (internal plugin aggregation)
├── config.js                # default config + redirect/replay/size limits
├── interceptors/            # HTTP pipeline stages (auth, timing, redirect, rate-limit, …)
└── lib/
    ├── webex-plugin.js      # WebexPlugin base class
    ├── stateless-webex-plugin.js
    ├── storage/             # makeWebexStore/makeWebexPluginStore + MemoryStoreAdapter
    ├── credentials/         # Credentials, Token, scope helpers
    ├── services/            # Services, ServiceCatalog (kept in core intentionally)
    └── webex-http-error.js  # WebexHttpError
```

## Key Files (source of truth)
| File | Holds |
|---|---|
| `packages/@webex/webex-core/src/webex-core.js` | `WebexCore` class, interceptor ordering (`preInterceptors`/`postInterceptors`), credential normalization, upload/logout/measure |
| `packages/@webex/webex-core/src/index.js` | the exact public export surface |
| `packages/@webex/webex-core/src/config.js` | default config values and limits (redirects, auth replays) |
| `packages/@webex/webex-core/src/lib/webex-plugin.js` | `WebexPlugin` base class (derived storage/config/logger) |
| `packages/@webex/webex-core/src/interceptors/auth.js` | `AuthInterceptor` request/response/replay behavior |

## Public Surface
This module is consumed as an imported library/package. Exact export list: `src/index.js`.

| Contract ID | Type | Surface | Purpose | Compatibility / deprecation | Schema / detail link | Root index |
|---|---|---|---|---|---|---|
| `webex-core.default` | SDK | `WebexCore` (default export) + `WebexCore.init(attrs)` | root SDK class other packages extend | stable; additive | `src/webex-core.js` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |
| `webex-core.registerPlugin` | SDK | `registerPlugin(name, constructor, options)` | register a public plugin onto core | stable | `src/webex-core.js` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |
| `webex-core.registerInternalPlugin` | SDK | `registerInternalPlugin(name, constructor, options)` | register an internal plugin | stable | `src/webex-core.js` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |
| `webex-core.WebexPlugin` | SDK | `WebexPlugin` base class | base for all plugins | stable | `src/lib/webex-plugin.js` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |
| `webex-core.storage` | SDK | `makeWebexStore`, `makeWebexPluginStore`, `MemoryStoreAdapter` | storage factories + default adapter | stable | `src/lib/storage` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |
| `webex-core.credentials` | SDK | `Credentials`, `Token`, `Services`, `ServiceCatalog` | auth + service discovery | stable | `src/lib/credentials`, `src/lib/services` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |
| `webex-core.interceptors` | SDK | `AuthInterceptor`, `RedirectInterceptor`, `PayloadTransformerInterceptor`, … | reusable pipeline stages | stable | `src/interceptors/*` | [`CONTRACTS.md`](../../../../ai-docs/CONTRACTS.md) |

Compatibility notes:
- Adding an export is a minor change; removing/renaming one is a major (breaking) change under semver.

## Requires (dependencies)
- Internal: `@webex/http-core` (HTTP + interceptor base, `HttpStatusInterceptor`), `@webex/common`
  (`proxyEvents`, `retry`, `transferEvents`), `@webex/common-timers`, `@webex/storage-adapter-spec`.
- External: `ampersand-state` `^5.0.3`, `ampersand-events`, `ampersand-collection`, `lodash`,
  `uuid` `^3.3.2`, `jsonwebtoken`, `crypto-js`, `core-decorators`.
- External services: Webex platform (service discovery, auth `/token`) — fail-closed on missing credentials.

## Requirements
| ID | WHAT | WHY | Source Evidence | Test / Example Evidence | Assumptions / Gaps | Confidence |
|---|---|---|---|---|---|---|
| `WEBEX-CORE-R-001` | `WebexCore.init(attrs)` merges default config with provided attributes and returns an initialized instance whose plugins are set up. | Callers need a single entry that produces a ready SDK instance. | `src/webex-core.js`, `src/index.js` | `None found` (host-deferred; not measured) | test evidence not verified in assess-only | PRESENT |
| `WEBEX-CORE-R-002` | The constructor normalizes multiple credential input formats (string token, several `credentials.*` paths) into `credentials.supertoken`, validating/correcting a bearer access token via `bearerValidator`. | Consumers pass tokens in varied shapes; core must standardize them before use. | `src/webex-core.js` (constructor, `bearerValidator`) | `None found` | — | PRESENT |
| `WEBEX-CORE-R-003` | Plugins register via `registerPlugin`/`registerInternalPlugin`, adding the plugin to the `_children` tree, merging its config, proxying named methods, and appending its interceptors and payload transforms. | Extensibility: every feature plugin composes onto core through one registration contract. | `src/webex-core.js` | `None found` | — | PRESENT |
| `WEBEX-CORE-R-004` | Requests run through an ordered interceptor pipeline: pre-interceptors, core interceptors, then post-interceptors. | Cross-cutting concerns (tracking, timing, auth, transform, status) apply uniformly to every request. | `src/webex-core.js` (`preInterceptors`, `interceptors`, `postInterceptors`) | `None found` | ServiceInterceptor/ConversationInterceptor are `undefined` placeholders filled by plugins | PRESENT |
| `WEBEX-CORE-R-005` | The `AuthInterceptor` adds an authorization header only when required, skips overwriting an existing one, handles 401 responses, and replays a request after refresh within the configured replay limit. | Authenticated requests must carry a valid token and recover from expiry without unbounded retry. | `src/interceptors/auth.js`, `src/config.js` (`maxAuthenticationReplays`) | `None found` | — | PRESENT |
| `WEBEX-CORE-R-006` | Core exposes bounded and unbounded storage (derived properties) backed by a configurable adapter, defaulting to `MemoryStoreAdapter`. | Plugins need namespaced storage without owning a backend. | `src/webex-core.js` (derived), `src/lib/storage` | `None found` | — | PRESENT |
| `WEBEX-CORE-R-007` | Child plugin `change:*` events bubble to the parent with a namespace prefix, and `ready` recomputes from `loaded` and child readiness. | A single readiness/event surface for the whole SDK instance. | `src/webex-core.js` (`derived.ready`), source doc §Event System | `None found` | — | PRESENT |
| `WEBEX-CORE-R-008` | `upload()` performs a three-phase upload (initialize → upload → finalize) and aborts when a size limit is exceeded. | Large-file handling with session lifecycle and a safety cap. | `src/webex-core.js` (`upload`, `_uploadPhase*`, `MAX_FILE_SIZE_IN_MB`) | `None found` | — | WEAK |

## Design Overview
Core is an AmpersandState model (`WebexCore = AmpState.extend(...)`) with `WebexInternalCore` as a
child. Construction first normalizes credentials, then `initialize()` merges config, wires loaded/ready
event handlers and child event propagation, builds the interceptor chain from the `interceptors` map
plus the `preInterceptors`/`postInterceptors` ordering arrays, creates the configured request function,
and generates a session id. Plugins are mixed in via `mixinWebexCorePlugins` /
`mixinWebexInternalCorePlugins`; each registered plugin is instantiated by AmpersandState and its
`initialize()` runs. `Services` is deliberately kept inside core (not a standalone internal plugin) so it
initializes before credentials — otherwise early requests would miss federation/service-discovery URLs
(`src/index.js` header comment).

`Webex.init(attrs)` in the unified `webex` package merges default configuration with the provided
attributes, sets `sdkType: 'webex'`, creates the instance by extending `WebexCore`, and auto-requires
the public plugins (`@webex/plugin-authorization`, `@webex/plugin-meetings`, `@webex/plugin-people`,
`@webex/plugin-rooms`, `@webex/plugin-messages`, and others).

`registerPlugin(name, constructor, options)` accepts `proxies` (methods proxied to the root level),
`interceptors` (HTTP interceptors added to the pipeline), `config` (merged into the main config),
`payloadTransformer.predicates`, `payloadTransformer.transforms`, and `onBeforeLogout` cleanup handlers.
Public plugins register on `WebexCore`; internal plugins register on `WebexInternalCore`. Plugin
lifecycle: registration (constructor added to `_children`) → instantiation by AmpersandState →
`initialize()` → namespace-specific config → `ready`.

Beyond construction, `WebexCore` exposes `refresh()` (delegates to `credentials.refresh()` for token
refresh), `transform(direction, object)` and `applyNamedTransform(...)` (apply payload transforms
bidirectionally / by name), `setConfig(newConfig)` (runtime config merge), `getWindow()`, `inspect()`
(debug representation), `measure()` (sends metrics via the metrics plugin), `logout()` (runs
`onBeforeLogout` handlers in reverse order, clears bounded and unbounded storage, invalidates
credentials, and emits `client:logout`), and `upload()` — a three-phase process
(`_uploadPhaseInitialize` → `_uploadPhaseUpload` → `_uploadPhaseFinalize`) with `_uploadApplySession`
and `_uploadAbortSession` (aborts when the file exceeds `MAX_FILE_SIZE_IN_MB`). `WebexInternalCore`
adds only `inspect()` and a derived `ready` that aggregates the readiness of all internal plugins.

Default configuration lives in `src/config.js` and holds the default configuration values: redirect and
replay limits (`maxAppLevelRedirects: 10`, `maxLocusRedirects: 5`, `maxAuthenticationReplays: 1`),
service-discovery URLs (`services.discovery`), storage-adapter configuration (`storage`), interceptor
settings (an optional `config.interceptors` override consumed when building the pipeline), payload
transform configuration (`payloadTransformer`), and security/domain-validation settings
(`services.validateDomains`). Configuration merges in order — default config loaded from
`config.js`, plugin configs merged during registration, user-provided config merged during
initialization, then runtime updates via `setConfig()`.

## Data Flow
Data enters as a `webex.request(options)` call, flows through the interceptor pipeline in order, out to
Webex services over HTTP (via `@webex/http-core`), and the response flows back through the post
interceptors before returning to the caller. Transport is HTTP(S).

```mermaid
flowchart LR
  Caller[plugin/webex.request] --> Pre[pre-interceptors: ResponseLogger, RequestTiming, RequestEvent, WebexTrackingId, RateLimit]
  Pre --> CoreI[core interceptors: Service, UserAgent, WebexUserAgent, Auth, PayloadTransformer, Redirect]
  CoreI --> HTTP[@webex/http-core fetch]
  HTTP --> Post[post-interceptors: HttpStatus, NetworkTiming, Embargo, RequestLogger, RateLimit]
  Post --> Caller
```

## Sequence Diagram(s)
Sequence coverage:

| Operation group | Diagram | Failure / recovery coverage |
|---|---|---|
| SDK initialization | Init & plugin bring-up | n/a (init failure surfaces to caller) |
| Authenticated request | Request pipeline with auth | 401 → refresh → replay (bounded) shown as alt branch |

```mermaid
sequenceDiagram
  participant App
  participant Webex as Webex.init
  participant Core as WebexCore
  participant Plugins
  App->>Webex: init({credentials})
  Webex->>Core: constructor (normalize credentials)
  Core->>Core: initialize (config, events, interceptor chain, sessionId)
  Core->>Plugins: instantiate + initialize each registered plugin
  Plugins-->>Core: ready
  Core-->>App: ready (loaded && all plugins ready)
```

```mermaid
sequenceDiagram
  participant Caller
  participant Pipeline as Interceptor pipeline
  participant Auth as AuthInterceptor
  participant Svc as Webex services
  Caller->>Pipeline: request(options)
  Pipeline->>Auth: onRequest
  Auth->>Auth: requiresCredentials?
  alt requires + no header set
    Auth->>Svc: request with Authorization
    alt 401 and replays remaining
      Svc-->>Auth: 401
      Auth->>Auth: refresh + replay (<= maxAuthenticationReplays)
      Auth->>Svc: replay request
    end
  else header already set / not required
    Auth->>Svc: request as-is
  end
  Svc-->>Caller: response (via post-interceptors)
```

Key interceptor behaviors verified against code: `AuthInterceptor` (`src/interceptors/auth.js`) implements
`onRequest()` (adds the authorization header), `requiresCredentials()` (determines whether auth is
needed), `onResponseError()` (handles 401 responses), `shouldAttemptReauth()` (decides on a token
refresh), and `replay()` (retries the failed request after refresh, bounded by
`maxAuthenticationReplays`). `RequestTimingInterceptor` measures request duration and adds timing
metadata. `PayloadTransformerInterceptor` applies bidirectional data transformations by delegating to
`webex.transform('outbound'|'inbound', …)`; encryption/decryption is not performed by core itself but by
transform functions that feature plugins register (for example the encryption plugin).

## Class / Component Relationships
```mermaid
classDiagram
  AmpersandState <|-- WebexCore
  AmpersandState <|-- WebexPlugin
  WebexCore *-- WebexInternalCore : child
  WebexCore o-- WebexPlugin : _children (registered plugins)
  WebexCore ..> Interceptor : builds pipeline
  Interceptor <|-- AuthInterceptor
  WebexCore ..> MemoryStoreAdapter : default storage
```
`WebexCore` and `WebexPlugin` both extend AmpersandState. Core composes `WebexInternalCore` as a child
and holds registered plugins in `_children`. Interceptors extend the `@webex/http-core` `Interceptor`
base; core builds concrete instances via each interceptor's `.create()`.

`WebexPlugin` provides the shared plugin API: `initialize()` (datatype binding plus child `change:`
propagation), `clear()` (clears plugin state while preserving the `parent` reference), `inspect()`,
`request()` and `upload()` (delegate to `webex.request()` / `webex.upload()`), `when()` (promise-based
event waiting), and `_filterSetParameters()` (normalizes `set()` parameters). Its derived properties
are `boundedStorage`, `unboundedStorage`, `config` (plugin-specific / namespace-scoped), `logger`, and
`webex` (reference to the root Webex instance); its session properties are `parent` (parent object
reference) and `ready` (plugin readiness status). The People plugin is a representative example
(`namespace: 'People'`; `children: { batcher: PeopleBatcher }`; `get(person)` retrieves a person by id;
`list(options)` lists people with filters; `inferPersonIdFromUuid(id)` converts a UUID to a Hydra id;
and `_getMe()` fetches the current user, decorated with `@oneFlight`).

## Use Cases
- **UC-1 Initialize the SDK:** app calls `Webex.init({credentials})` → core normalizes credentials →
  initializes config/events/pipeline → plugins load → instance becomes `ready`. Evidence:
  `src/webex-core.js`, `src/index.js`.
- **UC-2 Make an authenticated request:** a plugin calls `this.request()` → delegates to
  `webex.request()` → pipeline runs → `AuthInterceptor` adds the token → response returns. Evidence:
  `src/webex-core.js`, `src/interceptors/auth.js`.
- **UC-3 Access namespaced storage:** `webex.boundedStorage.get(key)` resolves the adapter, namespaces
  the key, and performs the operation. Evidence: `src/lib/storage`.
- **UC-4 Invoke a plugin method:** `webex.people.get('personId')` → `WebexCore` resolves the `people`
  child → `People.get()` executes → the plugin delegates via `this.request()` to `webex.request()` →
  the full interceptor pipeline runs → the plugin processes the response and returns the result.
  Evidence: `src/lib/webex-plugin.js`, `packages/@webex/plugin-people/src/people.js`.
- **Storage operations:** storage instances are created by `makeWebexStore(type, webex)` and
  `makeWebexPluginStore(type, plugin)`. Two storage types exist — bounded (size-limited, typically for
  frequently accessed data) and unbounded (no size limits, for archival data) — each backed by a
  configurable adapter, defaulting to `MemoryStoreAdapter` (in-memory). The optional
  `LocalStorageAdapter` (browser localStorage) and `SessionStorageAdapter` (browser sessionStorage) are
  provided by separate storage-adapter packages, not built into webex-core. Adapters expose `get(key)`
  (retrieve value), `set(key, value)` (store value), `del(key)` (delete value), and `clear()` (clear all
  values); a storage read resolves the adapter, namespaces the key, performs the operation, processes
  the raw result if needed, and returns the final value. Evidence: `src/lib/storage/index.js`,
  `src/config.js`.
<!-- module.crosses_service_boundaries = true: cross-service flow is the authenticated-request pipeline in UC-2. -->

## State Model
<!-- module.holds_client_state = true -->
`WebexCore` is an AmpersandState instance. Session properties: `config`, `loaded`, `request`,
`sessionId`. Derived properties: `boundedStorage`, `unboundedStorage`, `ready`. Base events: `loaded`
(data loaded from storage), `ready` (all plugins initialized), `change:config`, `client:logout`. Child
plugins hold their own state and bubble `change:<plugin>` events upward; `ready` is derived from
`loaded` and every child's readiness.

Event methods follow the AmpersandState/events API: `trigger(event, ...args)` (emit an event),
`listenTo(target, event, handler)`, `listenToAndRun(target, event, handler)` (listen and run
immediately), `stopListening(target, event, handler)`, and `when(event)` (promise-based event waiting).
Child events bubble to the parent with a namespace prefix (for example `change:people` when the People
plugin changes); plugins emit `change` events on state changes, the parent automatically propagates
them, and ready-state changes trigger a parent `ready` recalculation.

## Business Rules & Invariants
<!-- module.enforces_domain_rules = true -->
- Credential normalization order is significant and must be preserved (constructor path list in `src/webex-core.js`).
- An existing `authorization` header is never overwritten by `AuthInterceptor`; a null/false authorization is deleted to avoid a CORS preflight (`src/interceptors/auth.js`).
- Auth replays are bounded by `maxAuthenticationReplays`; redirects by `maxAppLevelRedirects`/`maxLocusRedirects` (`src/config.js`).
- `Services` must initialize before credentials — it is kept inside webex-core for this reason (`src/index.js`).

## Concurrency & Reactive Flow
<!-- module.is_concurrent_async = true -->
The request pipeline and event system are promise-based and event-driven. Interceptors return promises
and must not block; readiness and change events propagate reactively through the AmpersandState tree.
Ordering within the pipeline is guaranteed by the `preInterceptors`/core/`postInterceptors` arrays; auth
refresh/replay is bounded to avoid unbounded retry loops.

## Error Handling & Failure Modes
<!-- module.returns_caller_errors = true -->
| Condition | Signal (error/code/result) | Caller recovery |
|---|---|---|
| HTTP error status | `WebexHttpError` (via `HttpStatusInterceptor`) | inspect error; retry only if safe |
| 401 unauthorized | handled in `AuthInterceptor.onResponseError` → refresh + replay | automatic within replay limit; surfaces if exhausted |
| Upload exceeds size cap | abort session (`_uploadAbortSession`) | reduce payload below `MAX_FILE_SIZE_IN_MB` |
| Missing required credentials | request fails closed | supply valid credentials |

## Pitfalls
- Reordering the interceptor arrays changes cross-cutting behavior (auth before transform, status handling in post) — treat ordering as a contract.
- `ServiceInterceptor`, `ConversationInterceptor`, and `KmsDryErrorInterceptor` are `undefined` placeholders in the map, filled by plugins; assuming they are always present will break.
- `uuid` is pinned to `^3.3.2`; the v3 API differs from later majors — do not upgrade blindly.
- Browser vs Node divergence via `.shim.js` overrides: changing the Node file only can silently break the browser build.

## Module Do's / Don'ts
<!-- module.module_specific_conventions = true -->
- DO register new cross-cutting behavior as an interceptor via `.create()` and place it in the correct pipeline phase.
- DO extend `WebexPlugin` for new plugins and use `registerPlugin`/`registerInternalPlugin`.
- DON'T overwrite an existing `authorization` header or bypass credential normalization.
- DON'T move `Services` out of webex-core.

## Export Stability
<!-- module.published_package = true -->
Published as `@webex/webex-core` on the internal npm registry. Exports in `src/index.js` are the semver
surface: adding an export is a minor change; removing or renaming one is a major (breaking) change.

## Test-Case Strategy (module)
Unit tests run via Jest (`test:unit`), integration via Mocha (`test:integration`), and browser tests
via Karma (`test:browser`). Positive-and-negative coverage should assert both that a token is added when
required and that an existing/absent authorization header is respected, and that auth replay stops at
the configured limit. Concrete existing-test mapping was not measured in this assess-only pass
(host-deferred coverage).

| Behavior / Requirement | Existing test evidence | Gap |
|---|---|---|
| `WEBEX-CORE-R-002` (credential normalization) | `None found` (not measured) | verify positive + malformed-token cases |
| `WEBEX-CORE-R-005` (auth add/skip/replay) | `None found` (not measured) | verify replay-limit negative case |
| `WEBEX-CORE-R-004` (pipeline ordering) | `None found` (not measured) | verify pre/core/post ordering |

## Traceability
- Repo architecture: [`ARCHITECTURE.md`](../../../../ai-docs/ARCHITECTURE.md) · Registry: [`SPEC_INDEX.md`](../../../../ai-docs/SPEC_INDEX.md)
- Coverage state & contracts baseline: `.sdd/manifest.json`
