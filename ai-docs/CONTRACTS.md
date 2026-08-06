<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: contracts@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Contracts Catalog — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this root contract index; detailed contracts live with owning modules. Machine source `.sdd/manifest.json`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before adding any public-facing surface — check here first. Machine source of truth: `.sdd/manifest.json`.
> Do not inline large schemas here.

This repo is a published SDK consumed as a library, so its primary public surface is exported package APIs
and realtime events, not owned HTTP endpoints. The detailed per-package surface is owned by each module spec
(generated in the host module-spec phase); this catalog is the compact root index.

### Exported API & Types
<!-- Kept: the repo is imported as a library/package -->
| Contract ID | Owner module/package | Symbol | Signature | Stability / deprecation | Schema / detail link | Defined at |
|---|---|---|---|---|---|---|
| `webex.init` | `packages/webex` | `Webex.init(attrs)` | `init(attrs): Webex` | stable semver surface | `packages/webex/ai-docs/webex-spec.md` | `packages/webex/src/webex.js` |
| `webex-core.registerPlugin` | `packages/@webex/webex-core` | `registerPlugin(name, ctor, opts)` | plugin registration | stable | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core` |
| `calling.createClient` | `packages/calling` | calling SDK entry | standalone SDK surface | stable | `packages/calling/ai-docs/calling-spec.md` | `packages/calling/src` |
| `byods` | `packages/byods` | BYoDS SDK entry | standalone SDK surface | stable | `packages/byods/ai-docs/byods-spec.md` | `packages/byods/src` |
| `contact-center` | `packages/@webex/contact-center` | contact-center SDK entry | standalone SDK surface | stable | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` | `packages/@webex/contact-center/src` |

> Per-plugin exported symbols (people, rooms, messages, meetings, presence, webhooks, …) are cataloged in
> each plugin's module spec `Public Surface` during the host module-spec phase; this table lists the
> repo-level SDK entry points confirmed from code during onboarding.

### Events
<!-- Kept: the repo publishes/consumes events/messages -->
| Contract ID | Owner module | Event / topic | Direction | Payload schema link | Delivery guarantees | Compatibility / deprecation | Defined at |
|---|---|---|---|---|---|---|---|
| `webex-core.ready` | `packages/@webex/webex-core` | `ready` | publish | module spec | in-process event | stable | `packages/@webex/webex-core` |
| `webex-core.loaded` | `packages/@webex/webex-core` | `loaded` | publish | module spec | in-process event | stable | `packages/@webex/webex-core` |
| mercury events | `packages/@webex/internal-plugin-mercury` | server events over websocket | consume | module spec | realtime, connection-dependent | stable | `packages/@webex/internal-plugin-mercury` |

> Detailed event names/payloads are owned by the mercury/llm/voicea and consuming plugin specs.

## Requires — what this repo depends on
| Dependency (service / package / datastore) | What is consumed | Schema / detail link | Availability assumption | Fallback on failure | Version floor |
|---|---|---|---|---|---|
| Webex platform services | REST APIs + Mercury websocket | owning plugin specs | assumed available at runtime | per-plugin retry / 401 re-auth in `webex-core` | service-versioned |
| `webex-release-npm` registry | published/consumed packages | `GETTING_STARTED.md` | build-time | build fails | n/a |

## Compatibility & Deprecation Policy
- **Breaking-change rule:** No breaking change to an exported package surface without a major semver bump;
  version synchronization across workspace packages is managed by `@webex/package-tools`.
- **Deprecation:** deprecated exports are marked in the owning package and retained across at least one
  minor release before removal (repo convention; confirm per package in the host module-spec phase).

## Maintenance
- When a public surface is added/changed/removed, update this catalog, the owning module spec summary, and
  `.sdd/manifest.json` in the same change.
- Cross-reference: domain terms → `GLOSSARY.md`.
