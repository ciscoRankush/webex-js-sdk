# CONTRACTS — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md).
> Context-efficiency: this is the public-surface index. Exact schemas/signatures live in the native
> reference sources linked below — this file points to them, it does not copy them.

> **SDD status: assess-only draft (non-authoritative).** Public surfaces are indexed, not fully
> enumerated. Native/generated API reference remains the exact source of truth for signatures.

## Public surface model

The SDK's public surface is the set of exported plugin APIs composed onto a `webex` instance, plus the
standalone SDK entry points. Each package exposes its API from `src/index.[jt]s`. There is **no
OpenAPI/proto/GraphQL/AsyncAPI schema** in this repo — the SDK is a client library, and its "contract"
is the exported TypeScript/JS API surface plus the generated API reference docs.

## Native / generated reference sources (kept in place — not migrated to markdown)

| Contract source | What it covers | Location | Kept as source? |
|---|---|---|---|
| Generated API reference (documentation.js) | Public unified-SDK API (`webex`, plugins, resources, typedefs) | `docs/api/` (built by `yarn build:docs`) | ✅ native/generated — linked, not copied |
| API reference TOC config | Which classes/types appear in the reference | `documentation/config.yml`, `documentation/webex.md` | ✅ native source input |
| Presence typedoc | `@webex/plugin-presence` typed API | `docs/presence/` | ✅ generated typedoc |
| Per-package `build:docs` (typedoc) | `calling`, `byods`, `contact-center`, `plugin-encryption`, `plugin-presence` typed APIs | each package's docs output | ✅ generated typedoc |
| TypeScript declarations | Compile-time contract | per-package `types/` / `dist/` (`tsconfig.json`, api-extractor) | ✅ generated |

To regenerate: `yarn build:docs`.

## Public API entry points (index)

| Surface | Entry | Notes |
|---|---|---|
| Unified browser SDK | `packages/webex` (`webex`) | `Webex.init(attrs)`; auto-composes public plugins |
| Unified Node SDK | `packages/webex-node` (`webex-node`) | Node build of the unified SDK |
| Calling SDK | `packages/calling` (`@webex/calling`) | Standalone; typedoc reference |
| BYODS SDK | `packages/byods` (`@webex/byods`) | Standalone; typedoc reference |
| Contact Center | `packages/@webex/contact-center` | Standalone; typedoc reference (`typedoc.md`) |
| Public plugins | `packages/@webex/plugin-*` | Attach to `webex` (meetings, people, rooms, messages, memberships, teams, team-memberships, webhooks, attachment-actions, authorization*, encryption, presence, logger, device-manager) |
| Shared utilities (imported) | `packages/@webex/common` (`@webex/common`) | Internal utility surface — no network contract; exported JS API is the contract (barrel `src/index.js`). Includes the Hydra public-ID codec (`constructHydraId`/`deconstructHydraId` → `ciscospark://…` format) consumed SDK-wide. See `packages/@webex/common/ai-docs/common-spec.md` |
| Shared utilities (imported) | `packages/@webex/common-evented` (`@webex/common-evented`) | Internal utility surface — no network contract. Single default export: the `evented` class-property decorator; converts a property into a getter/setter that emits `change:<prop>` (`value`, `previous`) and generic `change` events (requires an `ampersand-events`-style `trigger` on the decorated class). See `packages/@webex/common-evented/ai-docs/common-evented-spec.md` |
| Shared utilities (imported) | `packages/@webex/common-timers` (`@webex/common-timers`) | Internal utility surface — no network contract. Three exports: `safeSetTimeout`/`safeSetInterval` (`unref`-aware `setTimeout`/`setInterval` wrappers that don't wedge a Node process open) and the restartable `Timer` class (`init → running → done` lifecycle with fail-loud `start`/`reset`/`cancel` guards). See `packages/@webex/common-timers/ai-docs/common-timers-spec.md` |

Each plugin's detailed public surface (methods, events, config namespace) belongs in that module's
spec (`<module-path>/ai-docs/<module-name>-spec.md`), generated in rigorous mode via `doc-backfill`.

## Routed contract-relevant source docs

| Doc | Owning module | Use |
|---|---|---|
| `packages/@webex/contact-center/typedoc.md` | contact-center | Package overview / API intro — routed to module spec + linked here |
| `packages/@webex/plugin-encryption/developer-quickstart.md` | plugin-encryption | Getting-started/API usage — routed to module spec |
| `packages/@webex/plugin-authorization/OAUTH-FLOW-GUIDE.md` (+ browser/node) | authorization plugins | OAuth public flow — routed to module specs; see `SECURITY.md` |

## Requires — what this repo depends on

The SDK is a client library; its outward dependencies are the Webex platform services it calls (resolved
at runtime via service discovery) plus the npm packages it builds on. There is no committed schema for
the remote services — the contract is the live API surface constrained by service discovery / domain
validation (`services.discovery`, `services.validateDomains` in `@webex/webex-core`).

| Dependency (service / package / datastore) | What is consumed | Schema / detail link | Availability assumption | Fallback on failure | Version floor |
|---|---|---|---|---|---|
| Webex platform services | Messaging, meetings, presence, KMS/encryption, identity/OAuth REST APIs | live API via `services.discovery` catalog | Assume available; discovery + `validateDomains` constrain hosts | `AuthInterceptor` 401 → refresh/replay (bounded); redirects bounded by `maxAppLevelRedirects`/`maxLocusRedirects` | n/a (hosted service) |
| Webex Identity Broker (OAuth) | Authorization endpoints, `/access_token` token exchange/refresh | `webexapis.com/v1/access_token`; routed OAuth guides | Assume available | Re-authenticate via `initiateLogin()` when refresh impossible | n/a |
| `ampersand-state` | Base state/model layer for `WebexCore`/`WebexPlugin` | package API | Pinned peer | — | per `package.json` |
| `@webex/http-core` | Low-level HTTP execution under the interceptor pipeline | workspace package | Workspace-synced | — | version-synced |
| npmjs registry | Resolves all build/runtime deps | `.yarnrc.yml` | Public registry, no auth secret | `checksumBehavior: update` | — |

## Compatibility & Deprecation Policy
- **Breaking-change rule:** No breaking change to an exported public surface without a **major** semver
  bump plus a consumer-facing changelog entry (`standard-version` via `yarn release`). Adding an optional
  field/parameter or a new export is a minor; removing or renaming an exported symbol is a major.
- **Deprecation:** Mark a surface deprecated in its JSDoc/typedoc and the changelog before removal; keep
  it for at least one minor release with a documented replacement before a major bump removes it.
- Published to npmjs as the unified `webex` / `webex-node` bundles and the modular `@webex/*` packages.
  See `ARCHITECTURE.md` → Release & Versioning.

## Maintenance
- When a public surface is added/changed/removed, update this catalog, the owning module spec's Public
  Surface section, any native/generated API reference (`yarn build:docs`), and `.sdd/manifest.json` in the
  same change.
- For an incompatible change, record the consumer transition/deprecation plan in the owning module spec
  and summarize it in the compatibility notes above.
- Cross-reference: routed OAuth/quickstart/typedoc source docs are enumerated in `.sdd/manifest.json`
  `spec_sources` and migrated by meaning into the owning module specs
  (`<module-path>/ai-docs/<module-name>-spec.md`).
