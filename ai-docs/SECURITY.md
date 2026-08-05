<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: security@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
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
| SDK → Webex service edge | remote Webex REST/realtime services | SDK client | HTTPS transport; auth header attached by `AuthInterceptor`; response status handling |
| Consumer → SDK init | consuming application input | SDK | credential shape normalization + bearer validation in `WebexCore` |

## Authentication & Authorization Model
- **Authentication:** OAuth access/super tokens normalized in `WebexCore.constructor` and validated by
  `bearerValidator`; attached to requests by `AuthInterceptor`
  (`packages/@webex/webex-core/src/webex-core.js`, `packages/@webex/webex-core/src/interceptors/auth.js`).
- **Authorization:** Authorization is enforced server-side by Webex services; the SDK carries the token
  and handles 401 by refresh + replay (`shouldAttemptReauth`, `replay`).
- **Default posture:** fail-closed when credentials are missing (`requiresCredentials`).

## Secret & Credential Handling
- Secrets source: supplied by the consuming application/environment at `Webex.init`; never in source.
- Injection: passed as `credentials`/`access_token` attrs; normalized onto `credentials.supertoken`.
- Rotation: `[NEEDS HUMAN INPUT]` — token refresh exists (`refresh()`), but a rotation policy was not evidenced.
- **Hard rule:** never commit secrets, tokens, keys, or connection strings; never log them.

## Data Classification & Handling
| Data class | Examples | Storage rule | Logging rule | In transit |
|---|---|---|---|---|
| Auth credentials | access/super tokens | in-memory / configured storage adapter only | never logged | TLS |
| Feature domain data | people, rooms, messages | cached via storage adapters | avoid logging PII | TLS |

## Input Validation & Output Encoding Posture
- Validate at the boundary (allow-list where possible); the bearer token is validated/corrected before
  use. Payload transforms (encryption/decryption) run in `PayloadTransformerInterceptor`.

<!-- Include if: the repo exposes a network/HTTP API or web surface -->
## Transport & Headers
- HTTPS/TLS to Webex services; tracking-id and user-agent headers added by interceptors
  (`packages/@webex/webex-core/src/interceptors/`). CORS/CSRF posture is governed by the Webex services,
  not this client.

<!-- Include if: the repo has known security-sensitive areas or accepted risks -->
## Known Sensitive Areas & Accepted Risks
| Area | Risk | Mitigation / why accepted | Owner |
|---|---|---|---|
| Credential normalization | many accepted token input shapes | centralized in `WebexCore.constructor`; validated by `bearerValidator` | `[NEEDS HUMAN INPUT]` |

## Reporting & Review
- Suspected vulnerabilities: Cisco Webex developer support (`devsupport@webex.com`, per `README.md`).
- Cross-reference: module-specific security behavior lives in the owning module spec and native
  threat-model or security-review source when one exists.
