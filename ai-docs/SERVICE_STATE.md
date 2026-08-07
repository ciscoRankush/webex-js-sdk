<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: service-state@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->

# Service State (living) — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Read this FIRST before adding a surface; stable contracts in `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Source of truth for "does X already exist?" Keep current in the same change that adds/removes a surface.

> Scope note: this repo is a **client SDK**, not a running service. It exposes no HTTP endpoints or
> datastores of its own; the "as-built" surface here is the published package/plugin surface and the
> external services/dependencies it consumes. Endpoint/rate-limit/SLO sections that only apply to a
> running service are marked N/A with that reason.

## Current Events
<!-- Include-if the service publishes or consumes events -->
| Event / topic | Direction | Producer/consumer | Payload ref |
|---|---|---|---|
| Mercury websocket events | consume | `internal-plugin-mercury` → plugins | `packages/@webex/internal-plugin-mercury/src/` |
| Per-plugin change events | publish (to consumer) | public `plugin-*` EventEmitters | each `plugin-*/src/` |

## Data Stores
<!-- Include-if the service owns or uses data stores -->
| Store | Purpose | Owned by this service? |
|---|---|---|
| Storage adapters (localForage/localStorage/sessionStorage) | Persist client state per the storage-adapter contract | Provided by the SDK; backed by the host browser/environment (SDK does not own a server datastore) |
| In-memory services registry / credentials | Resolved service URLs and token state | Yes (in-SDK, in-memory only) |

## External Dependencies
| Dependency | Used for | Timeout / retry | Circuit breaker / fallback |
|---|---|---|---|
| Webex cloud services | All capability APIs | per-plugin | per-plugin error surfacing |
| Mercury websocket | Server-pushed events | reconnect/backoff | reconnect in `internal-plugin-mercury` |
| internal npm registry | artifact resolution/publish | n/a (build-time) | none (blocks publish) |

## Feature Flags (current)
N/A — no repo-owned live feature-flag registry was found in committed evidence. Runtime feature gating
for the SDK is driven by server-delivered features via `internal-plugin-feature`, not a static flag
table owned by this repo.

| Flag | Gates | Current default | Owner | Safe to remove when |
|---|---|---|---|---|
| — | No repo-owned static feature flags; runtime features resolved via `internal-plugin-feature` | — | — | — |

## Compliance / Certifications
- Content confidentiality is enforced via KMS encryption (`internal-plugin-encryption`); tokens and decrypted content must never be logged. Formal certification obligations are `[NEEDS HUMAN INPUT]`.

<!-- N/A sections (client SDK, not a running service): Current Endpoints — the SDK exposes no HTTP endpoints of its own; Rate Limits & Quotas — enforced by upstream Webex services, not this client; Key Metrics & Performance Targets — no service SLOs (telemetry emitted via internal-plugin-metrics). -->

## Maintenance
- Update the relevant row in the same change that adds/changes/removes a surface, dependency, limit, or flag.
- Cross-reference: stable contracts → `CONTRACTS.md`; security posture → `SECURITY.md`.
