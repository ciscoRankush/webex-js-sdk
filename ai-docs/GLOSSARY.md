<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: glossary@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Glossary — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; related: `CONTRACTS.md`, `SECURITY.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read this before naming anything. Use the canonical name exactly; never introduce a synonym.

## Domain Terms

| Term | Definition (one or two sentences) | Authoritative location (file/type) | Notes / synonyms to avoid |
|---|---|---|---|
| supertoken | The consolidated access + refresh token bundle set on `webex.credentials` after a successful grant. | `plugin-authorization-*/src/authorization.js` (`credentials.set({supertoken})`) | not "session token" |
| clientType | Config value selecting the OAuth flow; `'confidential'` → Authorization Code, anything else → Implicit. | `plugin-authorization-browser/src/config.js` | not "grant type" |
| Implicit Grant | Public-client flow using `response_type=token`; token returned in URL hash, no refresh token. | `plugin-authorization-browser/src/authorization.js` (`initiateImplicitGrant`) | — |
| Authorization Code Grant | Flow using `response_type=code`; code exchanged for tokens (server-side or via PKCE). | `.../src/authorization.js` (`initiateAuthorizationCodeGrant`, `requestAuthorizationCodeGrant`) | — |
| PKCE | Proof Key for Code Exchange; first-party generates an S256 `code_challenge` and single-use `code_verifier`. | `plugin-authorization-browser-first-party/src/authorization.js` (`_generateCodeChallenge`) | — |
| CSRF token | UUID v4 stored in sessionStorage and embedded in OAuth `state`, verified on redirect return. | `.../src/authorization.js` (`_generateSecurityToken`, `_verifySecurityToken`) | not "nonce" |
| Device Authorization (QR) | First-party device-code flow: obtain user/device codes from oauth-helper and poll for the token. | `plugin-authorization-browser-first-party/src/authorization.js` (`initQRCodeLogin`, `_startQRCodePolling`) | not "polling login" |
| preauth catalog | Best-effort service catalog prefetch keyed by `emailhash` or `orgId` during first-party login. | `.../src/authorization.js` (`collectPreauthCatalog`) | — |
| IdBroker | Webex identity broker that authenticates users and issues OAuth codes/tokens. | referenced in flow guides | not "auth server" generically |

## Abbreviations & Acronyms

| Abbreviation | Expansion | Meaning in this repo |
|---|---|---|
| CSRF | Cross-Site Request Forgery | The state-bound token protecting the OAuth redirect. |
| PKCE | Proof Key for Code Exchange | S256 verifier/challenge used by the first-party flow. |
| JWT | JSON Web Token | Guest/user token created via `createJwt` and exchanged for an access token. |
| SDK | Software Development Kit | The Webex JS SDK these plugins extend. |
| QR | Quick Response (code) | The device-authorization login path in the first-party plugin. |

## Context-Specific Meanings

| Term | Context / module | Meaning here |
|---|---|---|
| `requestAuthorizationCodeGrant` | node & first-party | node exchanges `{code}` with client secret; first-party exchanges `{code, codeVerifier}` with PKCE. |
| `initiateLogin` | browser vs first-party | browser chooses implicit/code by `clientType`; first-party always builds a PKCE Authorization Code request. |

## Maintenance

- When a new OAuth concept, event, or state is introduced, add it here in the same change.
- Cross-reference: public-surface terms → `CONTRACTS.md`; security controls → `SECURITY.md`.
