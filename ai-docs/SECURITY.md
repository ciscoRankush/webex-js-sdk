<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
  source_material: BROWSER-OAUTH-FLOW-GUIDE.md, NODE-OAUTH-FLOW-GUIDE.md, webex-plugin-architecture.md, developer-quickstart.md
-->

# SECURITY.md — webex-js-sdk

> Security architecture and constraints for the Cisco Webex JavaScript SDK.

## trust boundaries

The SDK operates across the following trust boundaries:

| Boundary | Description | Risk |
|---|---|---|
| **Consumer app → SDK** | Application code calling SDK APIs. The SDK trusts caller-supplied access tokens and config values at face value. | Misconfigured or leaked tokens affect all SDK operations |
| **SDK → Webex platform services** | All outbound HTTPS/WSS calls. The SDK presents an OAuth bearer token; Webex platform enforces authorization. | Token leakage gives full access to the user's Webex account |
| **Browser ↔ Webex Identity Broker** | OAuth redirect flow. The browser receives tokens in the URL hash fragment. | Token interception if redirect URI is not HTTPS or registered |
| **SDK ↔ KMS** | Encryption key fetch. Executed over HTTPS; keys are not persisted client-side beyond the session. | Unauthorized KMS access would expose decrypted content |
| **Mercury WebSocket** | Persistent authenticated WSS connection for push events. | A compromised token allows an attacker to receive all push events for that user |
| **Node.js server ↔ SDK** | Server-side usage where `client_secret` and `access_token` live in the same process. | `client_secret` leakage allows impersonation of the OAuth application |

**What is outside this SDK's trust boundary:**
- The Webex backend services (platform-side authorization is out of scope)
- End-user device security (OS-level keychain, browser storage sandboxing)
- Network transport security below TLS (the SDK does not implement certificate pinning)

## authentication & authorization model

- **OAuth 2.0** is the sole supported authentication mechanism for user-context operations.
- **Browser implicit grant** (`clientType: 'public'`, `response_type=token`) — suitable only for SPAs with no server-side component. Access token arrives in the URL hash; must be immediately extracted and removed from the URL.
- **Authorization code grant** (`clientType: 'confidential'`, `response_type=code`) — recommended for applications with a server backend. `client_secret` must remain server-side.
- **Client credentials grant** — server-to-server, bot/service accounts, no user context.
- **JWT authentication** (Node.js) — guest users sign a JWT with a guest issuer credential (server-side only) and exchange it for a Webex access token.
- **Personal Access Tokens (PATs)** — development and testing only; never use in production.
- **CSRF protection** — the SDK generates and validates a `state` parameter on all browser-initiated OAuth redirects. Do not remove this check.
- **Token refresh** — `AuthInterceptor` in `@webex/http-core` automatically triggers token refresh on 401 responses. Plugins do not need to implement their own refresh logic.

## secret & credential handling

- **`client_secret` is server-side only.** Must never be included in browser bundles or client-side code.
- **Access tokens must not be logged.** `plugin-logger` must be configured to redact credential fields before logging. [NEEDS HUMAN INPUT — verify `plugin-logger` redaction configuration]
- **No secrets in source.** CodeRabbit `gitleaks` is enabled on all PRs; any committed secret will block merge.
- **Environment variables** (`WEBEX_ACCESS_TOKEN`, `WEBEX_CLIENT_SECRET`, etc.) go in `.env` files (gitignored) for development, GitHub Actions secrets for CI, and a secrets manager for production.
- **If a secret is accidentally committed:** immediately rotate the secret in the issuing system, remove it from git history with `git filter-repo`, and notify the security team.
- **Guest issuer credentials** (for JWT auth) must be stored server-side and never exposed to browser clients.
- **Refresh tokens** must not be stored in `sessionStorage` — use `localStorage` or secure server-side storage.

## data classification & handling

| Data class | Examples | Handling |
|---|---|---|
| **Credentials** | Access tokens, refresh tokens, `client_secret`, JWT issuer keys | Never logged, never in source, server-side where possible |
| **Encrypted content** | E2E-encrypted messages, files | Decrypted in-memory only; keys fetched from KMS at runtime, not persisted |
| **PII** | User display names, email addresses, phone numbers | Transmitted via TLS; not additionally encrypted by the SDK beyond platform E2E |
| **Metadata** | Room IDs, activity IDs, timestamps | Treated as internal; not transmitted to third parties by the SDK |
| **Telemetry** | Operational metrics, call quality data | Sent to `internal-plugin-metrics` endpoint; no credential data included |
| **Logs** | SDK debug/info/warn/error output | Must have credentials redacted before emission; log level configurable |

[NEEDS HUMAN INPUT] — Data residency and compliance considerations (GDPR, HIPAA, FedRAMP) for Contact Center deployments.

## input validation & output encoding posture

- **API response data** is consumed as-is from Webex platform services (trusted Cisco endpoints over HTTPS). No additional sanitization is applied by default.
- **HTML content from messages** — `@webex/helper-html` provides HTML sanitization helpers. Applications rendering message content as HTML must use this sanitizer or an equivalent.
- **File names and URLs from API responses** must be validated before use in `fetch`/`XMLHttpRequest` to prevent open-redirect or SSRF in consumer applications.
- **No `eval` or `new Function`** — ESLint `no-eval: error` is enforced across the codebase.
- **OAuth `state` parameter** — validated on redirect callback; reject responses with missing or mismatched state.
- **JWT payloads** are decoded with `jwt-decode` or `jose` (no verification in `jwt-decode`). Server-side token verification is the responsibility of the Webex Identity Broker, not the SDK client.

## reporting & review

- **Security issues** should be reported via Cisco's responsible disclosure program, not as public GitHub issues.
- **Dependency security audits** run on a monthly cadence using the script in `docs/dependency/audit-process.md`. Results are recorded in `docs/dependency/unmaintained-catalog.md`.
- **`dependabot.yml`** under `.github/` provides automated dependency update PRs.
- **CodeRabbit** runs `gitleaks` (secret detection) and `semgrep` (static analysis) on all PRs.
- **Zero-tolerance threshold:** deprecated + vulnerable critical-severity packages must be eliminated before merge.
- [NEEDS HUMAN INPUT] — Record completed security reviews, pen test findings, and CVE mitigations here with dates.

## Authentication and Authorization

*Evidence: `packages/@webex/plugin-authorization-browser/BROWSER-OAUTH-FLOW-GUIDE.md`, `packages/@webex/plugin-authorization-node/NODE-OAUTH-FLOW-GUIDE.md`*

- **OAuth 2.0** is the only supported authentication mechanism. Personal Access Tokens (PATs) are for development/testing only; never use them in production applications.
- **Browser implicit grant** (`clientType: 'public'`) returns access tokens directly via URL hash fragment — suitable only for single-page applications where the client secret cannot be protected.
- **Authorization Code Grant** (`clientType: 'confidential'`) is the secure flow for applications with a backend server. Requires `client_secret` on the server side; the secret must never be exposed to the browser.
- **CSRF protection** is built into the browser OAuth flow. The SDK generates and validates a state parameter on all browser-initiated redirects.
- **JWT authentication** (Node.js) is used for guest/bot access. The JWT is signed with a guest issuer credential and exchanged for a Webex access token. The guest issuer secret must be stored server-side only.

## Token Storage

- Access tokens and refresh tokens are stored via pluggable storage adapters:
  - Browser: `@webex/storage-adapter-local-storage` or `@webex/storage-adapter-local-forage` (IndexedDB/WebSQL)
  - Session: `@webex/storage-adapter-session-storage`
- **Never store refresh tokens in `sessionStorage`** — they should outlive the browser session; use `localStorage` or secure server-side storage.
- Tokens must never appear in URLs (access tokens from implicit grant arrive in the URL hash fragment and must be immediately extracted and removed from the URL before storing).

## Encryption (E2E)

*Evidence: `packages/@webex/plugin-encryption/developer-quickstart.md`, `webex-plugin-architecture.md`*

- End-to-end encryption uses the Webex KMS (Key Management Service). Encryption keys are fetched from KMS at runtime; they are never embedded in the SDK or stored in client-accessible storage.
- `webex.cypher.register()` must be called before any encrypted content operations.
- `webex.cypher.downloadAndDecryptFile()` handles file decryption using the KMS key for the file's space.
- The `spark:kms` scope must be requested during OAuth authorization for encryption features to work.
- `internal-plugin-encryption` handles low-level KMS protocol; `plugin-encryption` exposes the public `cypher` surface.

## Secret Handling Rules

- **`client_secret` is server-side only.** Never pass it to browser-side code or include it in a bundle.
- **Access tokens must not be logged.** `plugin-logger` must be configured to redact credentials before logging. [NEEDS HUMAN INPUT — verify logger redaction behavior]
- **No secrets in source.** CodeRabbit's `gitleaks` plugin is enabled on all PRs to detect secret patterns.
- **Environment variables** (`WEBEX_ACCESS_TOKEN`, `WEBEX_CLIENT_SECRET`, etc.) must never be committed. Use `.env` files (gitignored) in development; CI secrets for pipeline use.

## Dependency Security

*Evidence: `docs/dependency/audit-process.md`*

- Monthly unmaintained/suspect dependency audits using the audit script (`docs/dependency/audit-process.md`).
- A designated developer runs the script and records triage decisions in `docs/dependency/unmaintained-catalog.md`.
- Zero tolerance for `deprecated` + `vulnerable` critical packages.
- `dependabot.yml` is configured under `.github/` for automated dependency updates.

## Network Security

- All Webex API communication is over HTTPS/WSS (TLS).
- Mercury WebSocket connections are authenticated with the access token on connect.
- The SDK does not implement certificate pinning — standard platform TLS validation applies.

## Cross-Origin (Browser)

- The SDK is designed for same-origin or CORS-enabled Webex API endpoints.
- Redirect URIs for OAuth must be registered in the Webex developer portal and must use HTTPS in production.

## Known Constraints

- [NEEDS HUMAN INPUT] — Security review findings, pen test results, or known vulnerability mitigations should be recorded here with CVE references where applicable.
- [NEEDS HUMAN INPUT] — Data residency and compliance considerations (GDPR, HIPAA, etc.) for Contact Center deployments should be documented here.
