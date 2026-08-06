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
> This repo is a published SDK, not a deployed service; the as-built surface is its packages, events, stores,
> and outbound dependencies.

## Current Events
<!-- Kept: the SDK publishes/consumes events -->
| Event / topic | Direction | Producer/consumer | Payload ref |
|---|---|---|---|
| `ready` | publish | `webex-core` → consumer | module spec |
| `loaded` | publish | `webex-core` → consumer | module spec |
| `change:<namespace>` | publish | plugin → root instance | module spec |
| Mercury server events | consume | `internal-plugin-mercury` → plugins | module spec |

## Data Stores
<!-- Kept: the SDK uses pluggable client-side stores -->
| Store | Purpose | Owned by this service? |
|---|---|---|
| Bounded store | size-limited namespaced plugin state | yes (client-side, via storage adapter) |
| Unbounded store | archival plugin state | yes (client-side, via storage adapter) |
| Storage adapters | memory (default), localForage, localStorage, sessionStorage | yes (`@webex/storage-adapter-*`) |

> The SDK owns no server-side datastore (`repo.owns_datastore = false`); all stores are client-side.

## External Dependencies
| Dependency | Used for | Timeout / retry | Circuit breaker / fallback |
|---|---|---|---|
| Webex REST services | plugin API calls | per-plugin | 401 → bounded re-auth/replay (`maxAuthenticationReplays`) |
| Mercury websocket | realtime events | connection-managed | reconnect on drop |
| Mobius (calling) | calling registration/media | connection-managed | reconnect (see `calling` spec) |
| `webex-release-npm` registry | build/publish | build-time | build fails |

## Feature Flags (current)
| Flag | Gates | Current default | Owner | Safe to remove when |
|---|---|---|---|---|
| `internal-plugin-feature` toggles | server-driven feature gating | server-defined | platform | n/a (server-managed) |

> Per-plugin feature flags are resolved at runtime via `internal-plugin-feature`; enumerate in the owning
> module spec during the host module-spec phase.

## Maintenance
- Update the relevant row in the same change that adds/changes/removes a surface, dependency, or flag.
- Cross-reference: stable contracts → `CONTRACTS.md`; security posture → `SECURITY.md`.
