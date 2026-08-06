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
| Plugin | A workspace package that registers a capability or service onto `WebexCore`. | `packages/@webex/webex-core/src/` | Public = `plugin-*`; internal = `internal-plugin-*` |
| WebexCore | The core that owns the plugin registry, credentials, services registry, and request stack. | `packages/@webex/webex-core/src/` | not "the client" (that's an aggregate package) |
| Aggregate SDK | A package (`webex`, `webex-node`) bundling a curated plugin set into a ready-to-use SDK. | `packages/webex/src/`, `packages/webex-node/src/` | not "the core" |
| Mercury | The websocket transport delivering server-pushed events to the SDK. | `packages/@webex/internal-plugin-mercury/src/` | — |
| Locus | The meeting control-plane service/plugin. | `packages/@webex/internal-plugin-locus/src/` | — |
| Conversation | The encrypted messaging/activity model. | `packages/@webex/internal-plugin-conversation/src/` | — |
| KMS / encryption | Key-management and content encryption used by conversation. | `packages/@webex/internal-plugin-encryption/src/` | — |

## Abbreviations & Acronyms
| Abbreviation | Expansion | Meaning in this repo |
|---|---|---|
| SDK | Software Development Kit | The published Webex client library set |
| WxCC | Webex Contact Center | The `contact-center` standalone SDK domain |
| BYoDS | Bring Your Own Device Service | The `byods` SDK domain |
| WDM | Web Device Manager | Device registration/management (`internal-plugin-wdm`) |
| DSS | Directory Search Service | `internal-plugin-dss` |
| UMD | Universal Module Definition | Browser bundle format produced by `packages/webex` |

## Deprecated / Renamed Terms
<!-- Include-if the repo has deprecated/renamed concepts still present in older code -->
| Old term | Current term | Why renamed | Still appears in |
|---|---|---|---|
| — | — | No committed evidence of a renamed domain term in this assess-only turn | `[NEEDS HUMAN INPUT]` |

## Maintenance
- When a new domain concept is introduced (new entity, event, state), add it here in the same change.
- Cross-reference: public-surface terms → `CONTRACTS.md`.
