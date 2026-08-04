<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: glossary@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Glossary — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; related: `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read this before naming anything. Use the canonical name exactly; never introduce a synonym.

## Domain Terms
| Term | Definition (one or two sentences) | Authoritative location (file/type) | Notes / synonyms to avoid |
|---|---|---|---|
| WebexCore | The foundational class every SDK instance extends; owns config, request pipeline, storage, and the plugin tree. | `packages/@webex/webex-core/src/webex-core.js` | not "the SDK" (that's the `webex` unified package) |
| WebexInternalCore | Aggregates internal plugins and their readiness under core. | `packages/@webex/webex-core/src/webex-internal-core.js` | not "internal SDK" |
| Plugin | A unit of functionality registered onto core via `registerPlugin`/`registerInternalPlugin`, extending `WebexPlugin`. | `packages/@webex/webex-core/src/lib/webex-plugin.js` | not "module" (module = package here) |
| Interceptor | A request/response pipeline stage (pre/core/post) applied to every HTTP request. | `packages/@webex/webex-core/src/interceptors/` | not "middleware" |
| Services | Service-discovery component kept inside webex-core so it initializes before credentials. | `packages/@webex/webex-core/src/lib/services` | not "internal-plugin-services" |
| Bounded / Unbounded storage | Size-limited vs unlimited storage abstractions over a storage adapter. | `packages/@webex/webex-core/src/lib/storage` | — |

## Abbreviations & Acronyms
| Abbreviation | Expansion | Meaning in this repo |
|---|---|---|
| SDK | Software Development Kit | the published Webex JS packages consumers use |
| UMD | Universal Module Definition | the browser/CDN bundle format for the unified `webex` package |
| PKCE | Proof Key for Code Exchange | OAuth flow used by authorization plugins |

## Maintenance
- When a new domain concept is introduced (new plugin, interceptor, or exported type), add it here in the same change.
- Cross-reference: public-surface terms → `CONTRACTS.md`.
