<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: service-state@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Service State (living) — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Read this FIRST before adding a surface; stable contracts in `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Source of truth for "does X already exist?" Keep current in the same change that adds/removes a surface.

> This is a client SDK, not a running service: it exposes library surfaces and consumes remote services
> rather than hosting endpoints. The relevant "as-built" surfaces are exported APIs, events, and the
> storage/dependency posture below.

<!-- Include if: the service publishes or consumes events -->
## Current Events
| Event / topic | Direction | Producer/consumer | Payload ref |
|---|---|---|---|
| `loaded` | emit | `WebexCore` | data loaded from storage (`packages/@webex/webex-core/src/webex-core.js`) |
| `ready` | emit | `WebexCore` | all plugins initialized |
| `change:config` | emit | `WebexCore` | configuration changed |
| `client:logout` | emit | `WebexCore` | logout completed |
| `change:<namespace>` | emit | plugins → bubbles to `WebexCore` | plugin state change with namespace prefix |

## Data Stores
| Store | Purpose | Owned by this service? |
|---|---|---|
| boundedStorage | size-limited, frequently accessed data | yes (in-client) |
| unboundedStorage | unlimited/archival data | yes (in-client) |

Adapters: `MemoryStoreAdapter` (default), `LocalStorageAdapter`, `SessionStorageAdapter`
(`packages/@webex/webex-core/src/lib/storage`). The SDK does not own a server-side datastore.

## External Dependencies
| Dependency | Used for | Timeout / retry | Circuit breaker / fallback |
|---|---|---|---|
| Remote Webex REST/realtime services | all feature API calls | per request config; redirects bounded (`maxAppLevelRedirects: 10`) | 401 → refresh + replay (`maxAuthenticationReplays: 1`) |
| Identity provider (OAuth) | token issuance/refresh | `refresh()` | fail-closed on missing credentials |

<!-- Include if: the service enforces rate limits / quotas -->
## Rate Limits & Quotas
| Surface | Limit | Scope (per user/tenant/global) |
|---|---|---|
| request pipeline | `RateLimitInterceptor` handles server rate-limit responses | per client session |

## Feature Flags (current)
| Flag | Gates | Current default | Owner | Safe to remove when |
|---|---|---|---|---|
| `ENABLE_NETWORK_LOGGING` | request/response logger interceptors | OFF | `[NEEDS HUMAN INPUT]` | logging strategy finalized |
| `ENABLE_VERBOSE_NETWORK_LOGGING` | verbose network logging | OFF | `[NEEDS HUMAN INPUT]` | logging strategy finalized |

## Maintenance
- Update the relevant row in the same change that adds/changes/removes a surface, dependency, limit, or flag.
- Cross-reference: stable contracts → `CONTRACTS.md`; entities → `DATA_MODEL.md`; security posture → `SECURITY.md`.
