<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: architecture@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->

# ARCHITECTURE — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md). This is the system architecture; per-module detail lives in each manifest-routed module spec, source-local as `<module-path>/ai-docs/<module-name>-spec.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; this loads on demand, not upfront.

## Design Overview
webex-js-sdk is a client SDK organized as a **plugin architecture** on top of a shared core. The
design choice that explains its shape: capabilities are decomposed into independent workspace
packages that each register onto a single `WebexCore` instance, so a consumer composes only the
plugins it needs while transport, credentials, and service discovery stay centralized. `WebexCore`
(`packages/@webex/webex-core`) owns the plugin registry, credential/token lifecycle, the services
registry, and the request stack; every `plugin-*` and `internal-plugin-*` package layers domain
behavior on top of it.

The split between **public** `plugin-*` packages (the supported SDK surface: meetings, messages,
rooms, people, …) and **internal** `internal-plugin-*` packages (transport and service plumbing:
mercury websockets, locus meeting control-plane, encryption/KMS, device registration, …) keeps the
consumer-facing API separate from the moving internals. Aggregate entry packages (`packages/webex`
for browser/UMD, `packages/webex-node` for Node) bundle a curated plugin set into a ready-to-use
SDK. Two larger capabilities — `packages/calling` and `packages/@webex/contact-center` — ship as
standalone SDKs with their own migrated `ai-docs` spec trees.

Because it is a monorepo, shared build/test configuration lives in dedicated packages
(`packages/config/*`, `packages/legacy/*`) and tooling in `packages/tools/*`; the SDK owns no server
datastore and deploys no service of its own — it is published as npm packages consumed by browser
and Node applications.

## Component Inventory & Responsibilities
| Component | Responsibility (one line) | Docs |
|---|---|---|
| `packages/@webex/webex-core/` | Plugin framework, credentials, services registry, request stack | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| `packages/@webex/http-core/` | HTTP request/interceptor core underpinning plugins | `packages/@webex/http-core/ai-docs/http-core-spec.md` |
| `packages/@webex/internal-plugin-mercury/` | Mercury websocket transport | `packages/@webex/internal-plugin-mercury/ai-docs/internal-plugin-mercury-spec.md` |
| `packages/@webex/internal-plugin-locus/` | Meeting control-plane (locus) | `packages/@webex/internal-plugin-locus/ai-docs/internal-plugin-locus-spec.md` |
| `packages/@webex/internal-plugin-encryption/` | KMS/encryption for messaging/content | `packages/@webex/internal-plugin-encryption/ai-docs/internal-plugin-encryption-spec.md` |
| `packages/@webex/plugin-meetings/` | Public meetings SDK capability | `packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md` |
| `packages/@webex/plugin-messages/` · `plugin-rooms/` · `plugin-people/` | Public messaging/collaboration capabilities | see each module spec |
| `packages/webex/`, `packages/webex-node/` | Aggregate SDK entry points | `packages/webex/ai-docs/webex-spec.md`, `packages/webex-node/ai-docs/webex-node-spec.md` |
| `packages/calling/`, `packages/@webex/contact-center/` | Standalone calling and contact-center SDKs | `packages/calling/ai-docs/calling-spec.md`, `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| `packages/config/*`, `packages/legacy/*`, `packages/tools/*` | Shared build/test config and tooling | see each module spec |

> The full 93-module registry with per-module coverage state is in `SPEC_INDEX.md` and `.sdd/manifest.json`.

## Component Interaction
```mermaid
graph TD
  App[Consumer app] --> Agg[webex / webex-node aggregate]
  Agg --> Core[WebexCore plugin registry]
  Core --> HTTP[http-core request stack]
  Core --> Mercury[internal-plugin-mercury websocket]
  Core --> Pub[public plugin-* capabilities]
  Core --> Int[internal-plugin-* services]
  Pub --> Core
  Int --> Mercury
  Int --> HTTP
```
Consumer apps talk to an aggregate package, which instantiates `WebexCore`. Plugins register onto
core and reach services through the shared request stack (`http-core`) and the Mercury websocket for
server-pushed events. Public plugins delegate transport and encryption to internal plugins rather
than calling services directly.

## Execution & Flow
Representative flow (SDK init → capability call):
`new Webex(config)` → `WebexCore` boots, loads credentials and the services registry → registered
plugins initialize → a consumer calls a public plugin method (e.g. `webex.messages.create(...)`) →
the plugin builds a request through `http-core`, applying interceptors (auth token, tracking id) →
response resolves; server-pushed changes arrive over `internal-plugin-mercury` and are emitted as
plugin events. Grounded in the aggregate entry points (`packages/webex/src/`) and `WebexCore`
(`packages/@webex/webex-core/src/`).

## Dependencies
| Dependency | Type (internal / external / peer) | How used | Failure / version handling |
|---|---|---|---|
| Webex cloud services | external | The backends every plugin calls | Per-plugin error handling; SDK is a client, not the SoR |
| `@webex/webex-core` | internal | Plugin framework every plugin registers onto | Workspace version-synced |
| Mercury websocket service | external | Server-pushed events via `internal-plugin-mercury` | Reconnect/backoff in the mercury plugin |
| Babel / esbuild / webpack | external (build) | Transpile/bundle per package | Pinned in devDependencies (`package.json`) |
| Node runtime | external (peer) | Execution runtime | `engines.node` = `18.x` (`package.json`) |

### State Model
<!-- Include-if repo.holds_client_state = true -->
The SDK holds client-side session state rather than server data: `WebexCore` holds credentials/token
state and the services registry; individual plugins hold in-memory models (e.g. meeting state in
`plugin-meetings`, calling/registration state in `packages/calling`). Transitions are driven by
consumer method calls and by Mercury-delivered server events. Detailed per-plugin state models live
in the owning module specs (see `SPEC_INDEX.md`).

## Cross-Cutting Concerns
- **Security:** OAuth-based identity via the `plugin-authorization*` family (retained flow guides in
  each package); content/message encryption via `internal-plugin-encryption` (KMS). Never log tokens
  or key material. See `SECURITY.md`.
- **Observability:** telemetry/metrics via `internal-plugin-metrics`; structured logging via
  `plugin-logger`. Client-side error reporting and log upload via `internal-plugin-support`.

## Non-Functional Posture
As a published client library the key non-functional axes are bundle footprint (browser/UMD builds
via `packages/webex`) and API compatibility across the plugin surface. Semver governs the public
plugin packages; the aggregate entry points must stay consumable in both browser and Node targets.
Precise size budgets and SLOs are `[NEEDS HUMAN INPUT]` — not resolvable from committed evidence in
this assess-only turn.

<!-- ===== Conditional extras ===== -->

## Dependency / Interaction Topology
<!-- Include-if repo.components_interact = true -->
```mermaid
graph LR
  webex --> webex-core
  plugin-meetings --> webex-core
  plugin-meetings --> internal-plugin-locus
  plugin-meetings --> internal-plugin-mercury
  internal-plugin-conversation --> internal-plugin-encryption
  plugin-messages --> internal-plugin-conversation
  internal-plugin-mercury --> http-core
```
| From | To | Kind (call / event) | Purpose |
|---|---|---|---|
| `plugin-*` | `webex-core` | call | Register + resolve services, credentials, requests |
| `internal-plugin-mercury` | `plugin-*` | event | Deliver server-pushed changes |
| `plugin-messages` | `internal-plugin-conversation` | call | Encrypted message/activity operations |
| `internal-plugin-conversation` | `internal-plugin-encryption` | call | Encrypt/decrypt content via KMS |

## Object / Data Ownership
<!-- Include-if repo.domain_data_across_components = true -->
| Domain object | System-of-record (owning component) | Read by |
|---|---|---|
| Credentials / token | `webex-core` (credentials) | all plugins via core |
| Device registration | `internal-plugin-device` / `internal-plugin-wdm` | plugins needing a registered device |
| Conversation / activity | `internal-plugin-conversation` | `plugin-messages`, `plugin-rooms` |
| Meeting/locus state | `internal-plugin-locus` | `plugin-meetings` |

> The SDK owns no persistent server datastore; "ownership" here is the in-SDK writer of each domain model.

## Caching Catalog
<!-- Include-if repo.caches_data = true -->
| Cache | Backend | What it holds | TTL | Invalidation trigger |
|---|---|---|---|---|
| Services registry | in-memory (webex-core) | Resolved service URLs | session | Re-discovery / re-login |
| Storage adapters | localForage / localStorage / sessionStorage | Persisted client state per the storage-adapter-spec contract | adapter-defined | Explicit clear / logout |
| Pagination caches | in-memory (e.g. contact-center `PageCache`) | Paged result windows | operation-scoped | New query / eviction |

## Observability Patterns
<!-- Include-if repo.observability_convention = true -->
- **Logging:** structured logging via `plugin-logger`; never log tokens, key material, or message plaintext.
- **Metrics:** telemetry taxonomy via `internal-plugin-metrics` (and per-SDK metrics in `calling`/`contact-center`).
- **Audit:** client log upload / diagnostics via `internal-plugin-support`.

## Shared / Base Libraries
<!-- Include-if repo.shared_base_libs = true -->
| Library | What every module inherits from it | Version floor |
|---|---|---|
| `@webex/webex-core` | Plugin base class, credentials, services, request stack | workspace-synced |
| `@webex/common` | Shared mixins/decorators/utilities | workspace-synced |
| `@webex/http-core` | HTTP request/interceptor primitives | workspace-synced |
| `packages/config/*`, `packages/legacy/*` | Shared build/lint/test configuration | workspace-synced |

## Package Map & Inter-Package Dependencies
<!-- Include-if repo.is_monorepo = true -->
- **Workspace tooling:** Yarn `3.4.1` workspaces; globs from root `package.json`:
  `packages/@webex/*`, `packages/webex`, `packages/webex-node`, `packages/calling`, `packages/byods`,
  `packages/byods-demo-server`, `packages/config/*`, `packages/legacy/*`, `packages/tools/*`.
- **Package → responsibility + visibility:** `plugin-*` (public), `internal-plugin-*` (internal),
  `webex`/`webex-node` (public aggregate), `config/*` and `legacy/*` and `tools/*` (internal build),
  `test-helper-*` and `test-*` (internal test support). Full list in `SPEC_INDEX.md`.
- **Inter-package graph:** every plugin depends on `webex-core`; internal plugins depend on transport
  (`mercury`, `http-core`); aggregates depend on the curated plugin set. Build order bootstraps
  `webex-core` first (`package.json` `build`/`prebuild:modules`).
- **Version-sync rule:** workspace packages are released together via `standard-version`/package-tools; workspace-internal deps use `workspace:*`.

## Platform Matrix
<!-- Include-if repo.multi_platform = true -->
| Platform | Shared core vs per-platform | Entry / build | Notes |
|---|---|---|---|
| Browser | Shared plugins + browser field shims (`package.json` `browser`) | `packages/webex` UMD/webpack build | Karma-based browser tests |
| Node | Shared plugins + node entry | `packages/webex-node` | Node `18.x` |
| Mobile (samples/e2e) | Consumes browser build | WebdriverIO mobile config | e2e/sample only |

## Release & Versioning
<!-- Include-if repo.published_package = true -->
- Publish target: internal npm registry (`publishConfig.registry` in `package.json`,
  `engci-maven-master.cisco.com/.../webex-release-npm`).
- Semver per public package; releases coordinated across the workspace via `standard-version`.
- Deprecation/changelog: `standard-changelog`; consumer-facing changelog obligation on public plugins.

## Security Architecture
<!-- Include-if repo.security_arch_warranted = true -->
Identity flows through the `plugin-authorization*` family (OAuth; browser/node/first-party variants,
each with a retained flow guide). Message and content confidentiality is enforced by
`internal-plugin-encryption` (KMS-backed) consumed via `internal-plugin-conversation`. Tokens are
held in `webex-core` credentials and attached by request interceptors; they are never persisted in
source or logged. Detailed trust boundaries are in `SECURITY.md`.

---
→ Per-module orientation and detailed design live in each manifest-routed module spec, source-local as `<module-path>/ai-docs/<module-name>-spec.md`. Routing: `SPEC_INDEX.md`.

## Architecture Reference Links
| Reference | Location | When to read |
|---|---|---|
| Architecture decisions | `adr/` | To understand why major design choices were made and what alternatives were rejected |
| Repo patterns | `patterns/` | To follow established implementation conventions reflected in this architecture |
| Enforceable rules | `RULES.md` + `rules/` | To understand constraints every architecture-affecting change must obey |

## WS6 References
| WS6 artifact | Relevance to this repo | Link |
|---|---|---|
| — | No WS6 platform/enterprise architecture source available from committed evidence | `[NEEDS HUMAN INPUT]` |
