<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: security@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Security Baseline — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; module-specific security behavior lives in each owning module spec.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before changing anything that touches input, identity, data, or external calls. This doc also carries
> the dependency-audit posture migrated from `docs/dependency/*`.

## Trust Boundaries
| Boundary | Untrusted side | Trusted side | What is enforced at the crossing |
|---|---|---|---|
| SDK → Webex service edge | Webex REST/websocket services | SDK client | Bearer token attached by `AuthInterceptor`; TLS; response transform/decryption |
| Consumer app → SDK | consumer-provided input/config | SDK plugins | Input handled by plugins; access token supplied by consumer |
| Allowed-domain check | remote redirect/service URLs | `webex-core` | Domain allow-list matched on DNS label boundaries (recent fix `9e7042f`) |

## Authentication & Authorization Model
- **Authentication:** OAuth 2.0 via the `plugin-authorization*` family (browser implicit/auth-code, Node confidential client, first-party PKCE/QR); bearer token attached and validated in the `AuthInterceptor` (`packages/@webex/webex-core` interceptors; `packages/@webex/plugin-authorization*`).
- **Authorization:** delegated to Webex services; the SDK carries the caller's token scope. No local RBAC.
- **Default posture:** requests requiring credentials fail-closed when no valid token is present; 401 triggers a bounded re-auth/replay (`maxAuthenticationReplays`).

## Secret & Credential Handling
- Secrets source: the consumer's Webex access token (e.g. `WEBEX_ACCESS_TOKEN`), supplied at runtime — never source code.
- Injection: passed to `Webex.init({credentials})` / environment; held in the credentials model/storage.
- Rotation: token refresh handled by `credentials.refresh()`; long-term rotation is the consumer's responsibility ([NEEDS HUMAN INPUT] for org policy specifics).
- **Hard rule:** never commit or log tokens, keys, or connection strings.

## Data Classification & Handling
| Data class | Examples | Storage rule | Logging rule | In transit |
|---|---|---|---|---|
| Access credentials | OAuth access/refresh tokens | held in credentials/storage; not persisted in plaintext logs | never logged | TLS |
| User content | messages, meeting/conversation data | encrypted via encryption plugins where applicable | never log payloads | TLS |
| PII | person/email identifiers | not persisted server-side by SDK | masked/never logged | TLS |

## Input Validation & Output Encoding Posture
- Validate/normalize untrusted URLs and domains at the boundary (allow-list; DNS-label-boundary matching per `9e7042f`); HTML sanitized via `@webex/helper-html`. Encode output for its sink.

## Transport & Headers
<!-- Kept: the SDK makes network/HTTP + websocket calls -->
- HTTPS/TLS for all Webex service calls and Mercury/LLM websockets. Tracking and user-agent headers are added by core interceptors.

## Known Sensitive Areas & Accepted Risks
| Area | Risk | Mitigation / why accepted | Owner |
|---|---|---|---|
| Unmaintained dependencies | supply-chain risk | tracked via `docs/dependency/unmaintained-catalog.md` and periodic audit process; snapshots retained | platform team |
| Token handling in browser flows | token exposure | first-party PKCE + hardened flow for web client; guidance in OAuth flow guides (retained reference) | auth team |

## Reporting & Review
- Report suspected vulnerabilities via <https://developer.webex.com/support> / devsupport@webex.com.
- Security-relevant changes (auth, encryption, domain allow-listing, dependency audits) require security review.
- Cross-reference: module-specific security behavior lives in the authorization/encryption module specs; dependency-audit process migrated from `docs/dependency/audit-process.md`.
