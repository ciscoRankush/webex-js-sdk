<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: contracts@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->

# Contracts Catalog — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this root contract index; detailed contracts live with owning modules or canonical schema files. Machine source `.sdd/manifest.json`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before adding any public-facing surface — check here first. Machine source of truth: `.sdd/manifest.json`.
> Do not inline large schemas here.

### Exported API & Types
<!-- Include-if the repo is imported as a library/package -->
webex-js-sdk is consumed as a set of published npm packages; its public contract is the exported API
of the aggregate packages and public plugins. Detailed per-symbol surfaces live in each owning module
spec (`Public Surface`) and, where present, in native API-report/typedoc output. This assess-only
turn records the top-level entry surfaces; per-symbol detail is populated during the host coverage
phase.

| Contract ID | Owner module/package | Symbol | Signature | Stability / deprecation | Schema / detail link | Defined at |
|---|---|---|---|---|---|---|
| `webex.default-export` | `packages/webex` | `Webex` | `new Webex(config)` browser/UMD aggregate SDK | stable public semver surface | `packages/webex/ai-docs/webex-spec.md` | `packages/webex/src/` |
| `webex-node.default-export` | `packages/webex-node` | `Webex` | Node aggregate SDK | stable public semver surface | `packages/webex-node/ai-docs/webex-node-spec.md` | `packages/webex-node/src/` |
| `webex-core.plugin-api` | `packages/@webex/webex-core` | `WebexCore` / `registerPlugin` | Plugin registry + core services | stable internal-framework surface | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` | `packages/@webex/webex-core/src/` |
| `plugin-meetings.surface` | `packages/@webex/plugin-meetings` | `webex.meetings.*` | Meetings capability API | stable public semver surface | `packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md` | `packages/@webex/plugin-meetings/src/` |
| `calling.surface` | `packages/calling` | Calling SDK API | Calling capability API | stable public semver surface | `packages/calling/ai-docs/calling-spec.md` | `packages/calling/src/` |
| `contact-center.surface` | `packages/@webex/contact-center` | Contact Center SDK API | WxCC agent API | stable public semver surface | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` | `packages/@webex/contact-center/src/` |
| `storage-adapter.contract` | `packages/@webex/storage-adapter-spec` | StorageAdapter interface | Conformance contract implemented by the storage adapters | stable interface | `packages/@webex/storage-adapter-spec/ai-docs/storage-adapter-spec-spec.md` | `packages/@webex/storage-adapter-spec/src/` |

> The remaining public plugins each expose their capability under `webex.<capability>.*`; see the
> owning module spec's `Public Surface` for the per-symbol surface. Full per-symbol enumeration is
> `[NEEDS HUMAN INPUT]` / pending host coverage measurement.

### Events
<!-- Include-if the repo publishes or consumes events/messages -->
| Contract ID | Owner module | Event / topic | Direction (publish/consume) | Payload schema link | Delivery guarantees | Compatibility / deprecation | Defined at |
|---|---|---|---|---|---|---|---|
| `mercury.server-events` | `packages/@webex/internal-plugin-mercury` | Mercury websocket events | consume | `[NEEDS HUMAN INPUT]` (no committed schema file) | reconnect/re-delivery; not assume single delivery | additive | `packages/@webex/internal-plugin-mercury/src/` |
| `plugin.change-events` | public plugins | Per-capability change events | publish (to consumer) | see owning module spec | in-process EventEmitter | additive fields only | each `plugin-*/src/` |

## Requires — what this repo depends on
| Dependency (service / package / datastore) | What is consumed | Schema / detail link | Availability assumption | Fallback on failure | Version floor |
|---|---|---|---|---|---|
| Webex cloud services | REST + websocket capability APIs | `[NEEDS HUMAN INPUT]` (external service contracts) | assume available; client-side error handling | per-plugin retry/error surfacing | n/a |
| Mercury websocket | server-pushed events | see `internal-plugin-mercury` spec | assume reconnectable | backoff/reconnect | n/a |
| internal npm registry | `@webex/*` artifact resolution/publish | `GETTING_STARTED.md` → Artifact Registries | required for release | none (blocks publish) | n/a |

## Compatibility & Deprecation Policy
- **Breaking-change rule:** no breaking change to a public plugin/aggregate export without a major semver bump and a consumer transition note.
- **Deprecation:** mark deprecated surfaces in the owning module spec and changelog; keep for at least one minor deprecation window before removal.

## Detailed Interface Docs
- OAuth flows: retained native guides — `packages/@webex/plugin-authorization/OAUTH-FLOW-GUIDE.md`, `.../plugin-authorization-browser/BROWSER-OAUTH-FLOW-GUIDE.md`, `.../plugin-authorization-node/NODE-OAUTH-FLOW-GUIDE.md`.
- Storage adapter contract: `packages/@webex/storage-adapter-spec/README.md` + module spec.

## Maintenance
- When a public surface is added/changed/removed, update this catalog, the owning module spec summary, any canonical schema/API detail source, and `.sdd/manifest.json` in the same change.
- For incompatible changes, include the consumer transition/deprecation plan in the owning contract detail and summarize it in the Compatibility column.
- Cross-reference: domain terms → `GLOSSARY.md`.
