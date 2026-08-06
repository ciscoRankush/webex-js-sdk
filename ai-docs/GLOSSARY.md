<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: glossary@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Glossary — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; related: `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read this before naming anything. Use the canonical name exactly; never introduce a synonym.

## Domain Terms
| Term | Definition (one or two sentences) | Authoritative location (file/type) | Notes / synonyms to avoid |
|---|---|---|---|
| WebexCore | Foundational class providing plugin registration, HTTP pipeline, credentials, storage, and events that all plugins build on. | `packages/@webex/webex-core` | not "the SDK core class" loosely |
| WebexPlugin | Base class every plugin extends; carries `namespace`, config, logger, and `request()`. | `packages/@webex/webex-core` (`lib/webex-plugin`) | not "module" |
| Plugin | A workspace package (`plugin-*` public, `internal-plugin-*` internal) registered on the core. | `packages/@webex/*` | not "extension" |
| Interceptor | A stage in the HTTP request/response pipeline (auth, service, payload transform, timing, logging, rate limit). | `packages/@webex/webex-core` interceptors | not "middleware" |
| Mercury | The internal websocket transport delivering realtime server events. | `packages/@webex/internal-plugin-mercury` | not "socket" generically |
| Locus | Meeting/call state service model. | `packages/@webex/internal-plugin-locus` | — |
| Mobius | Backend the standalone calling SDK connects to. | `packages/calling` (`mobius-socket`) | — |
| Unified module | Consumer-facing bundle composing plugins (`webex`, `webex-node`). | `packages/webex`, `packages/webex-node` | not "the SDK" ambiguously |

## Abbreviations & Acronyms
| Abbreviation | Expansion | Meaning in this repo |
|---|---|---|
| SDK | Software Development Kit | The Webex JS client library set |
| DSS | Directory Search Service | `internal-plugin-dss` |
| WDM | Web Device Manager | `internal-plugin-wdm` |
| BYoDS | Bring Your Own Device Server | `packages/byods` |
| PKCE | Proof Key for Code Exchange | OAuth flow used by first-party browser auth |
| UMD | Universal Module Definition | CDN bundle format for `packages/webex` |

## Maintenance
- When a new domain concept is introduced (new entity, event, state), add it here in the same change.
- Cross-reference: public-surface terms → `CONTRACTS.md`.
