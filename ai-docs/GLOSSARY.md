<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: glossary@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Glossary — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; related: `DATA_MODEL.md`, `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read this before naming anything. Use the canonical name exactly; never introduce a synonym. Find a term
> in code that isn't here? Add it rather than guessing its meaning.

## Domain Terms
| Term | Definition (one or two sentences) | Authoritative location (file/type) | Notes / synonyms to avoid |
|---|---|---|---|
| WebexCore | The foundational class that provides plugin registration, HTTP pipeline, credentials, storage, config, and events. | `packages/@webex/webex-core/src/webex-core.js` | not "the SDK" (that is the unified `webex` package) |
| WebexPlugin | Base class every feature plugin extends; binds datatypes, config namespace, and delegates `request`/`upload`. | `packages/@webex/webex-core/src/lib/webex-plugin.js` | — |
| Interceptor | A stage in the HTTP request pipeline (pre/core/post) with `onRequest`/`onResponse` hooks. | `packages/@webex/webex-core/src/interceptors/` | not "middleware" |
| Plugin (public / internal) | A feature module registered on `WebexCore`/`WebexInternalCore` via `registerPlugin`/`registerInternalPlugin`. | `packages/@webex/webex-core/src/index.js` | — |
| Bounded / Unbounded storage | Size-limited vs unlimited storage created by `makeWebexStore`. | `packages/@webex/webex-core/src/lib/storage` | — |
| Supertoken | The normalized canonical credential shape tokens are moved onto during construction. | `packages/@webex/webex-core/src/webex-core.js` | — |

## Abbreviations & Acronyms
| Abbreviation | Expansion | Meaning in this repo |
|---|---|---|
| SDK | Software Development Kit | the Webex JS SDK; the unified package is `webex` |
| UMD | Universal Module Definition | the browser/CDN bundle format for `webex` |
| PKCE | Proof Key for Code Exchange | OAuth flow used by authorization plugins |

<!-- Include if: the repo has deprecated/renamed concepts still present in older code -->
## Deprecated / Renamed Terms
| Old term | Current term | Why renamed | Still appears in |
|---|---|---|---|
| ServiceCatalog | ServiceCatalogV2 / ServicesV2 | v2 service catalog added | `packages/@webex/webex-core/src/lib/services-v2` (both exported) |

## Maintenance
- When a new domain concept is introduced (new entity, event, state), add it here in the same change.
- Cross-reference: data entities → `DATA_MODEL.md`; public-surface terms → `CONTRACTS.md`.
