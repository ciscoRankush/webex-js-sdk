<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: service-state@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Service State (living) — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Read this FIRST before adding a surface; stable contracts in `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Source of truth for "does X already exist?" Keep current in the same change that adds/removes a surface.
> This repo is a client SDK (published packages), not a running service; sections below describe the
> as-built exported surface and outbound dependencies rather than server endpoints.

<!-- Include if: the service publishes or consumes events -->
## Current Events
| Event / topic | Direction | Producer/consumer | Payload ref |
|---|---|---|---|
| `loaded` | emit | `WebexCore` | data loaded from storage (`webex-core.js`) |
| `ready` | emit | `WebexCore` | all plugins initialized (`webex-core.js`) |
| `change:config` | emit | `WebexCore` | configuration changed |
| `client:logout` | emit | `WebexCore` | logout completed |
| `change:<plugin>` | emit | plugin → core | namespaced child change bubbled to parent |

<!-- Include if: the service owns or uses data stores -->
## Data Stores
| Store | Purpose | Owned by this service? |
|---|---|---|
| bounded storage | size-limited frequently accessed data | abstraction owned by core; backing adapter external |
| unbounded storage | archival data | abstraction owned by core; backing adapter external |
| `MemoryStoreAdapter` | default in-memory backing store | yes (`lib/storage`) |

## External Dependencies
| Dependency | Used for | Timeout / retry | Circuit breaker / fallback |
|---|---|---|---|
| Webex platform services | service discovery, auth `/token`, resource APIs | bounded auth replay (`maxAuthenticationReplays`) | fail-closed on missing credentials |
| `@webex/http-core` | HTTP request execution + status handling | `[NEEDS HUMAN INPUT]` | n/a (internal lib) |

## Feature Flags (current)
| Flag | Gates | Current default | Owner | Safe to remove when |
|---|---|---|---|---|
| `ENABLE_NETWORK_LOGGING` | request/response logger interceptors | OFF (unset) | `[NEEDS HUMAN INPUT]` | logging strategy finalized |
| `ENABLE_VERBOSE_NETWORK_LOGGING` | verbose network logging | OFF (unset) | `[NEEDS HUMAN INPUT]` | as above |

## Maintenance
- Update the relevant row in the same change that adds/changes/removes a surface, dependency, limit, or flag.
- Cross-reference: stable contracts → `CONTRACTS.md`; security posture → `SECURITY.md`.
