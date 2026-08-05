<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: contracts@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Contracts Catalog — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this root contract index; detailed contracts live with owning modules or canonical schema files. Machine source `.sdd/manifest.json`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before adding any public-facing surface — check here first. Machine source of truth: `.sdd/manifest.json`.
> Schema convention: prefer `.yaml` for OpenAPI/AsyncAPI unless this repo already standardizes on `.yml`; use
> `.proto`, `.graphql`, JSON Schema, or language-native SDK API outputs when those are the natural source.
> Do not inline large schemas here.

> Assess-only scope: this index covers the SDK's exported (library) surface for the tracked module
> `@webex/webex-core`. Feature-plugin exports are code-first (Untracked) and not yet cataloged here.

<!-- Include if: the repo is imported as a library/package -->
### Exported API & Types
| Contract ID | Owner module/package | Symbol | Signature | Stability / deprecation | Schema / detail link | Defined at |
|---|---|---|---|---|---|---|
| webex-core.default | `@webex/webex-core` | `WebexCore` (default export) | `class WebexCore extends AmpersandState` | stable public surface | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/index.js` |
| webex-core.registerPlugin | `@webex/webex-core` | `registerPlugin` | `registerPlugin(name, constructor, options)` | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/index.js` |
| webex-core.registerInternalPlugin | `@webex/webex-core` | `registerInternalPlugin` | `registerInternalPlugin(name, constructor, options)` | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/index.js` |
| webex-core.WebexPlugin | `@webex/webex-core` | `WebexPlugin` | `class WebexPlugin` base class | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/lib/webex-plugin.js` |
| webex-core.storage | `@webex/webex-core` | `makeWebexStore`, `makeWebexPluginStore`, `MemoryStoreAdapter` | store factory + adapters | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/lib/storage` |
| webex-core.credentials | `@webex/webex-core` | `Credentials`, `Token` | credential/token exports | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/lib/credentials` |
| webex-core.interceptors | `@webex/webex-core` | `AuthInterceptor`, `PayloadTransformerInterceptor`, … | interceptor class exports | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/interceptors/` |

## Requires — what this repo depends on
| Dependency (service / package / datastore) | What is consumed | Schema / detail link | Availability assumption | Fallback on failure | Version floor |
|---|---|---|---|---|---|
| Remote Webex REST/realtime services | authenticated API calls via the request pipeline | service discovery config | assumed available | 401 → refresh + replay; redirects bounded by config | Webex platform |
| `@webex/http-core` | HTTP transport + status interceptor | module spec | workspace-internal | n/a | workspace:* |
| Identity provider (OAuth) | token issuance/refresh | `SECURITY.md` | assumed available | fail-closed on missing credentials | n/a |

## Compatibility & Deprecation Policy
- **Breaking-change rule:** No breaking change to an exported symbol without a major semver bump for the
  owning package; additive fields/exports are minor.
- **Deprecation:** `[NEEDS HUMAN INPUT]` — a repo-wide deprecation window was not evidenced; individual
  packages follow semver.

<!-- Include if: a non-trivial interface needs full schema/error detail beyond this catalog -->
## Detailed Interface Docs
- Exact exported declarations for `@webex/webex-core` live in `packages/@webex/webex-core/src/index.js`
  and the module spec's Public Surface. Generated API reference is produced by the repo's
  `build:docs` documentation pipeline (`package.json`).

## Maintenance
- When a public surface is added/changed/removed, update this catalog, the owning module spec summary,
  any canonical schema/API detail source, and `.sdd/manifest.json` in the same change.
- For incompatible changes, include the consumer transition/deprecation plan in the owning contract detail and
  summarize it in the Compatibility / deprecation column.
- Cross-reference: domain terms → `GLOSSARY.md`; entities → `DATA_MODEL.md`.
