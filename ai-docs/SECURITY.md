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

> Read before changing anything that touches input, identity, data, or external calls. Don't weaken a
> documented control without an explicit, approved decision (record it as an ADR).

## Trust Boundaries
| Boundary | Untrusted side | Trusted side | What is enforced at the crossing |
|---|---|---|---|
| SDK ↔ Webex cloud services | remote services / network | SDK client | TLS transport; OAuth token attached by request interceptors (`http-core`) |
| Consumer app ↔ SDK | host application input | SDK plugins | Input validation at plugin method boundaries |
| Mercury websocket ↔ SDK | server-pushed event stream | internal plugins | Message parsing/validation in `internal-plugin-mercury` |
| Encrypted content boundary | ciphertext in transit/storage | decrypted in-memory | KMS-backed encrypt/decrypt via `internal-plugin-encryption` |

## Authentication & Authorization Model
- **Authentication:** OAuth via the `plugin-authorization*` family (browser / node / first-party variants), each with a retained flow guide (`packages/@webex/plugin-authorization*/…-OAUTH-FLOW-GUIDE.md`). Tokens are held in `webex-core` credentials.
- **Authorization:** access decisions are enforced server-side by Webex services; the SDK presents the bearer token. Client-side authorization logic is `[NEEDS HUMAN INPUT]` where a plugin adds its own gating.
- **Default posture:** the SDK is a client — it does not grant access; it authenticates the caller and relies on service-side authorization.

## Secret & Credential Handling
- Secrets source: OAuth client credentials/tokens supplied at runtime by the consuming application/environment — never source code.
- Injection: held in `webex-core` credentials and attached by request interceptors.
- Rotation: token refresh via `test-helper-refresh-callback`/authorization plugins; policy detail `[NEEDS HUMAN INPUT]`.
- **Hard rule:** never commit secrets, tokens, keys, or connection strings; never log them.

## Data Classification & Handling
| Data class | Examples | Storage rule | Logging rule | In transit |
|---|---|---|---|---|
| Credentials/tokens | OAuth access/refresh tokens | in-memory only (webex-core) | never logged | TLS |
| Message/content plaintext | conversation activities, files | decrypted in-memory only | never logged | encrypted (KMS) then TLS |
| Client persisted state | storage-adapter data | per storage-adapter contract | no secrets | n/a |

## Input Validation & Output Encoding Posture
- Validate at plugin boundaries (allow-list where possible); `@webex/helper-html` provides HTML sanitization for rendered/user content. Encode output for its sink.

## Transport & Headers
<!-- Include-if the repo exposes a network/HTTP API or web surface -->
- All service calls use HTTPS/TLS; request interceptors in `http-core` attach auth and tracking headers. Detailed header/CORS posture for browser builds is `[NEEDS HUMAN INPUT]`.

## Known Sensitive Areas & Accepted Risks
| Area | Risk | Mitigation / why accepted | Owner |
|---|---|---|---|
| Unmaintained dependencies | supply-chain risk from stale deps | tracked in the dependency audit process/catalog (retained under `docs/dependency/`) | `[NEEDS HUMAN INPUT]` |
| Encryption plugin | mishandled key material could expose content | isolated in `internal-plugin-encryption`; never log key material | `[NEEDS HUMAN INPUT]` |

## Reporting & Review
- Security-relevant changes require review by a code owner; suspected vulnerabilities should be reported through the project's security contact (`[NEEDS HUMAN INPUT]` — no committed SECURITY contact policy found beyond dependency audit docs).
- Cross-reference: module-specific security behavior lives in the owning module spec (authorization/encryption plugins) and the retained OAuth flow guides.
