# SECURITY — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md).

> **SDD status: assess-only draft (non-authoritative).** Posture below is grounded in
> `webex-plugin-architecture.md` (routed source), the OAuth flow guides, and committed config. Verify
> against code before relying on any statement.

## Trust Boundaries

The SDK is a **client library** that runs in the consumer's browser or Node process and calls Webex
platform services. The primary trust boundary is the network edge between the SDK-hosting app and Webex
backend services. The SDK does not host a server surface of its own (samples/demo servers excepted).

| Boundary | Untrusted side | Trusted side | What is enforced at the crossing |
|---|---|---|---|
| SDK → Webex services | SDK-hosting app / network | Webex backend | Service discovery + `services.validateDomains` constrain hosts; `AuthInterceptor` attaches credentials; TLS in transit |
| OAuth redirect (browser) | Redirect URL from identity broker | Browser plugin | CSRF `state` verified against `sessionStorage`; URL parsed then sanitized of sensitive params |
| Token exchange (Node) | OAuth callback query string | Node plugin / server | `state`/CSRF validation; code exchanged server-side using `client_secret`; error params checked |
| Encrypted payloads | Ciphertext on the wire | Encryption plugins / KMS | `PayloadTransformerInterceptor` decrypts; KMS-backed keys (`spark:kms` scope) |

## Authentication & Authorization Model
- **Authentication:** OAuth 2.0 against the Webex Identity Broker. `WebexCore` normalizes tokens across
  several input shapes; `bearerValidator()` validates/corrects bearer-token format
  (`packages/@webex/webex-core/src/webex-core.js`). Browser public clients use **Implicit Grant**
  (`response_type=token`); confidential clients and Node use **Authorization Code Grant**
  (`response_type=code`) with a `client_secret`. JWT and client-credentials flows exist for
  guest/server-to-server scenarios. *(Source: OAuth flow guides.)*
- **Authorization:** Access is scope-based (e.g. `spark:all`, `spark:kms`); the granted `scope` rides on
  the token. `AuthInterceptor.requiresCredentials()` decides when a request needs auth; `canAuthorize`
  reflects whether authenticated requests are possible.
- **Default posture:** Requests that require credentials are not sent authorized until credentials are
  present; on 401 the `AuthInterceptor` decides whether to re-auth, refreshes, and replays (bounded by
  `maxAuthenticationReplays`, default 1). *(Source: `webex-plugin-architecture.md`, `interceptors/auth.js`.)*

## Secret & Credential Handling
- Secrets source: environment / caller-supplied config — never source code. `.env` is git-ignored
  (`.gitignore`); `.env.default` documents variable **names** only (e.g. `WEBEX_CLIENT_SECRET`,
  `WEBEX_APPID_SECRET`, `WEBEX_SCOPE`).
- Injection: `WebexCore` accepts tokens/credentials at `Webex.init(...)`; `AuthInterceptor` injects the
  authorization header on outbound requests. Node keeps `client_secret` server-side (never shipped to a
  browser); the browser implicit flow uses no client secret.
- Rotation/refresh: tokens auto-refresh via `grant_type=refresh_token` (or a configured
  `refreshCallback`/`jwtRefreshCallback`); the browser persists tokens in browser storage, Node in secure
  server-side storage (DB/encrypted files/key store).
- **Hard rule:** never commit secrets, tokens, keys, or connection strings; never log them. Logging
  interceptors must redact authorization material.

## Data Classification & Handling
The SDK transports user content (messages, meeting data, presence, contacts) and identity/credential
material. Treat message bodies, tokens, and user identifiers as sensitive.

| Data class | Examples | Storage rule | Logging rule | In transit |
|---|---|---|---|---|
| Credentials | access/refresh tokens, `client_secret`, JWT | Browser storage (browser) / secure server-side store (Node); never in source | Never logged; redacted by interceptors | TLS |
| User content | message bodies, meeting/presence/contact data | Encryption plugins handle at-rest/e2e protection for services that require it | Never log content | TLS; encrypted payloads via `PayloadTransformerInterceptor` |
| Identifiers | user UUIDs / Hydra IDs | Per plugin storage namespace | Avoid logging | TLS |

## Input Validation & Output Encoding Posture
- Service hosts are constrained by service discovery and `services.validateDomains`; redirects are
  bounded (`maxAppLevelRedirects`, `maxLocusRedirects`) to prevent open-redirect/replay drift.
- Browser OAuth return URLs are parsed, error-checked, CSRF-validated, and then **sanitized** (sensitive
  params removed) during plugin `initialize()` / `_parseHash()`.
- CSRF tokens are generated with `uuid.v4()`, stored in `sessionStorage`, carried in the OAuth `state`,
  and verified on return (`_generateSecurityToken()` / `_verifySecurityToken()` in the browser plugin).

<!-- Include if: the repo exposes a network/HTTP API or web surface -->
## Transport & Headers
- HTTPS/TLS for all Webex service calls; token exchange/refresh POST to `webexapis.com/v1/access_token`.
- Browser flows rely on the identity broker + CORS handling for API requests; the SDK does not relax
  transport security for authed routes.

## Secret handling (implementation notes)
- The npm registry (`https://registry.npmjs.org`) is public and requires no committed auth secret.
- `PayloadTransformerInterceptor` applies bidirectional payload transforms including
  **encryption/decryption** for services that require it (`@webex/internal-plugin-encryption`,
  `@webex/plugin-encryption`). *(Source: `webex-plugin-architecture.md`, HTTP Request Pipeline.)*

## Reporting & Review
- Security-relevant changes (auth, credential handling, encryption, interceptor pipeline) require review
  by the owning maintainers before merge. Follow the repo's disclosure process
  (see `CONTRIBUTING.md` / Cisco security reporting) for vulnerabilities; do not file exploit detail in
  public issues.
- Cross-reference: module-specific security behavior lives in the owning module specs
  (`plugin-authorization*`, `plugin-encryption`), and the routed OAuth flow guides enumerated in
  `.sdd/manifest.json` `spec_sources`.

---
*Assess-only: rigorous mode should verify each claim against
`packages/@webex/webex-core/src/interceptors/auth.js`, the authorization plugins, and the encryption
plugins, and expand this into the full SECURITY template.*
