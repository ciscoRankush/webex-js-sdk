<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: security@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Security Baseline — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; module-specific security behavior lives in each owning module spec.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before changing anything that touches input, identity, data, or external calls. Don't weaken a
> documented control without an explicit, approved decision (record it as an ADR).

## Trust Boundaries
| Boundary | Untrusted side | Trusted side | What is enforced at the crossing |
|---|---|---|---|
| SDK → Webex services (network edge) | remote responses | SDK client | auth headers added, HTTP status handling, bounded redirect/replay (`config.js`) |
| App → SDK (token intake) | app-supplied credentials | `WebexCore` | credential normalization + `bearerValidator` (`webex-core.js`) |

## Authentication & Authorization Model
- **Authentication:** OAuth token-based. Credentials normalized in the `WebexCore` constructor and applied by the `AuthInterceptor` (`packages/@webex/webex-core/src/interceptors/auth.js`). Token refresh + request replay on 401 are bounded by `maxAuthenticationReplays` (`packages/@webex/webex-core/src/config.js`).
- **Authorization:** delegated to Webex platform services; the SDK carries tokens rather than making authorization decisions.
- **Default posture:** fail-closed on missing/invalid credentials for requests that require them (`AuthInterceptor.requiresCredentials`).

## Secret & Credential Handling
- Secrets source: OAuth tokens supplied at runtime by the consuming application — never source code.
- Injection: passed to `Webex.init({credentials})`; normalized and held in credential state.
- Rotation: token refresh handled via `credentials.refresh()`; broader rotation policy `[NEEDS HUMAN INPUT]`.
- **Hard rule:** never commit secrets, tokens, keys, or connection strings; never log them.

## Data Classification & Handling
| Data class | Examples | Storage rule | Logging rule | In transit |
|---|---|---|---|---|
| Credentials | OAuth access/refresh tokens | held in memory / configured storage adapter; not persisted in source | never logged | HTTPS to platform |
| User content | messages, people, rooms data | handled by feature plugins (Untracked) | `[NEEDS HUMAN INPUT]` | HTTPS |

## Input Validation & Output Encoding Posture
- Credentials are validated/corrected at intake (`bearerValidator`). Request/response payloads pass through the `PayloadTransformerInterceptor` for bidirectional transformation (incl. encryption/decryption where applicable).

<!-- Include if: the repo exposes a network/HTTP API or web surface -->
## Transport & Headers
- Transport security is provided by the Webex platform HTTPS endpoints. Tracking id and user-agent headers are added by dedicated interceptors (`WebexTrackingIdInterceptor`, `WebexUserAgentInterceptor`). CORS/CSRF specifics `[NEEDS HUMAN INPUT]`.

## Known Sensitive Areas & Accepted Risks
| Area | Risk | Mitigation / why accepted | Owner |
|---|---|---|---|
| Auth refresh/replay | replay loop on repeated 401 | bounded by `maxAuthenticationReplays` in `config.js` | `[NEEDS HUMAN INPUT]` |

## Reporting & Review
- Suspected vulnerabilities: <https://developer.webex.com/support> / devsupport@webex.com (per `README.md`).
- Cross-reference: module-specific security behavior lives in the owning module spec.
