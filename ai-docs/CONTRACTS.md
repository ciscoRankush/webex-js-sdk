<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: contracts@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Contracts Catalog — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this root contract index; detailed contracts live with owning modules or canonical schema files. Machine source `.sdd/manifest.json`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before adding any public-facing surface — check here first. Machine source of truth: `.sdd/manifest.json`.
> Do not inline large schemas here.

### Exported API & Types
<!-- The repo is imported as a library/package. Only @webex/webex-core is tracked in this pass. -->
| Contract ID | Owner module/package | Symbol | Signature | Stability / deprecation | Schema / detail link | Defined at |
|---|---|---|---|---|---|---|
| `webex-core.default` | `@webex/webex-core` | `WebexCore` (default export) | `WebexCore.init(attrs): WebexCore` | stable public surface (semver) | `packages/@webex/webex-core/ai-docs/webex-core-spec.md#public-surface` | `packages/@webex/webex-core/src/webex-core.js` |
| `webex-core.registerPlugin` | `@webex/webex-core` | `registerPlugin` | `registerPlugin(name, constructor, options)` | stable | same spec | `packages/@webex/webex-core/src/webex-core.js` |
| `webex-core.registerInternalPlugin` | `@webex/webex-core` | `registerInternalPlugin` | `registerInternalPlugin(name, constructor, options)` | stable | same spec | `packages/@webex/webex-core/src/webex-core.js` |
| `webex-core.WebexPlugin` | `@webex/webex-core` | `WebexPlugin` | base class for plugins | stable | same spec | `packages/@webex/webex-core/src/lib/webex-plugin.js` |
| `webex-core.storage` | `@webex/webex-core` | `makeWebexStore`, `makeWebexPluginStore`, `MemoryStoreAdapter` | storage factory + default adapter | stable | same spec | `packages/@webex/webex-core/src/lib/storage` |
| `webex-core.credentials` | `@webex/webex-core` | `Credentials`, `Token`, `Services`, `ServiceCatalog` | auth + service discovery exports | stable | same spec | `packages/@webex/webex-core/src/lib/credentials`, `src/lib/services` |
| `webex.default` | `webex` (unified) | `Webex` | unified SDK entry | `[NEEDS HUMAN INPUT]` (Untracked) | — | `packages/webex/src/index.js` |

> Public surfaces of the unified `webex` package and every `@webex/plugin-*` are **not yet cataloged**
> (those modules are `Untracked` in this assess-only pass). Confirming and cataloging them is
> [NEEDS HUMAN INPUT].

## Requires — what this repo depends on
| Dependency (service / package / datastore) | What is consumed | Schema / detail link | Availability assumption | Fallback on failure | Version floor |
|---|---|---|---|---|---|
| Webex platform services | service discovery, auth `/token`, resource APIs | native platform (external) | assume available | bounded auth replay/redirect (`config.js`) | n/a |
| `@webex/http-core` | HTTP primitives + interceptor base | package entry point | internal `workspace:*` | n/a | `workspace:*` |
| `ampersand-state` | state/model base | package entry point | external | n/a | `^5.0.3` |

## Compatibility & Deprecation Policy
- **Breaking-change rule:** public `@webex/*` exports follow semver; no breaking export change without a
  major bump. Detailed per-release deprecation windows: `[NEEDS HUMAN INPUT]`.
- **Deprecation:** `[NEEDS HUMAN INPUT]` — repo-wide deprecation marking convention not confirmed.

## Maintenance
- When a public surface is added/changed/removed, update this catalog, the owning module spec summary,
  and `.sdd/manifest.json` in the same change.
- Cross-reference: domain terms → `GLOSSARY.md`.
