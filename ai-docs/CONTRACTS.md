<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: contracts@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Contracts Catalog — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this root contract index; detailed contracts live with owning modules. Machine source `.sdd/manifest.json`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Read before adding any public-facing surface — check here first. Machine source of truth: `.sdd/manifest.json`.
> No OpenAPI/AsyncAPI/proto/GraphQL/JSON Schema source exists for these packages; the public surface is the SDK export API plus the first-party event emitter.

### Exported API & Types

Consumed as `webex.authorization.<method>` after `Webex.init(...)`. Methods available depend on the runtime/package selected by the auto-loader.

| Contract ID | Owner module/package | Symbol | Signature | Stability / deprecation | Schema / detail link | Defined at |
|---|---|---|---|---|---|---|
| `authorization.initiateLogin` | plugin-authorization-browser | `initiateLogin` | `initiateLogin(options?): Promise<void>` | stable public surface | module spec Public Surface | `plugin-authorization-browser/src/authorization.js` |
| `authorization.initiateImplicitGrant` | plugin-authorization-browser | `initiateImplicitGrant` | `initiateImplicitGrant(options): Promise<void>` (response_type=token) | stable | module spec | `plugin-authorization-browser/src/authorization.js` |
| `authorization.initiateAuthorizationCodeGrant` | plugin-authorization-browser / first-party | `initiateAuthorizationCodeGrant` | `initiateAuthorizationCodeGrant(options): Promise<void>` (response_type=code) | stable | module spec | `.../src/authorization.js` |
| `authorization.requestAuthorizationCodeGrant` | plugin-authorization-node / first-party | `requestAuthorizationCodeGrant` | `requestAuthorizationCodeGrant({code, codeVerifier?}): Promise` | stable | module spec | `plugin-authorization-node/src/authorization.js` |
| `authorization.requestAccessTokenFromJwt` | plugin-authorization-browser / node | `requestAccessTokenFromJwt` | `requestAccessTokenFromJwt({jwt}): Promise` | stable | module spec | `.../src/authorization.js` |
| `authorization.createJwt` | plugin-authorization-browser / node | `createJwt` | `createJwt({issuer, secretId, displayName?, expiresIn}): Promise<{jwt}>` | stable | module spec | `.../src/authorization.js` |
| `authorization.logout` | all impls | `logout` | `logout(options?): Promise\|void` | stable | module spec | `.../src/authorization.js` |
| `authorization.initQRCodeLogin` | plugin-authorization-browser-first-party | `initQRCodeLogin` | `initQRCodeLogin(): void` (device/QR flow) | internal-intended | module spec | `plugin-authorization-browser-first-party/src/authorization.js` |
| `authorization.cancelQRCodePolling` | plugin-authorization-browser-first-party | `cancelQRCodePolling` | `cancelQRCodePolling(withCancelEvent=true): void` | internal-intended | module spec | `.../src/authorization.js` |
| `authorization.isAuthorizing` | browser / node / first-party | `isAuthorizing` (prop) | boolean; `isAuthenticating` is an alias | stable | module spec | `.../src/authorization.js` |
| `authorization.ready` | browser / first-party | `ready` (prop) | boolean, set after redirect/code processing | stable | module spec | `.../src/authorization.js` |
| `default` / `config` exports | plugin-authorization | `default`, `config` | environment-selected re-export | stable | module spec | `plugin-authorization/src/index.js`, `src/index.browser.js` |

### Events

First-party plugin emits lifecycle events via `webex.authorization.eventEmitter` (names on `webex.authorization.Events`).

| Contract ID | Owner module | Event / topic | Direction (publish/consume) | Payload schema link | Delivery guarantees | Compatibility / deprecation | Defined at |
|---|---|---|---|---|---|---|---|
| `authorization.Events.login` | plugin-authorization-browser-first-party | `login` (eventType: `initiateLogin`, `redirectToLoginUrl`) | publish | `{eventType, data}` | best-effort in-process | internal-intended | `.../src/authorization.js` |
| `authorization.Events.qRCodeLogin` | plugin-authorization-browser-first-party | `qRCodeLogin` (eventType: `getUserCodeSuccess`/`getUserCodeFailure`/`authorizationPending`/`authorizationSuccess`/`authorizationFailure`/`pollingCanceled`) | publish | `{eventType, userData?, data?}` | best-effort in-process | internal-intended | `.../src/authorization.js` |

## Requires — what this repo depends on

| Dependency (service / package / datastore) | What is consumed | Schema / detail link | Availability assumption | Fallback on failure | Version floor |
|---|---|---|---|---|---|
| Webex IdBroker | OAuth authorize/token endpoints | native OAuth2 (no local schema) | assumed available | OAuth 400 → typed `grantErrors` | n/a |
| hydra (`jwt/login`) | JWT → access token exchange | — | assumed available | promise rejection propagated | n/a |
| oauth-helper | device authorize/token (QR) | — | assumed available (first-party) | `slow_down`/`428` handled; timeout → `authorizationFailure` | n/a |
| `webex.credentials` | token storage (`set({supertoken})`) | credentials plugin | required | n/a | `workspace:*` |

## Compatibility & Deprecation Policy

- **Breaking-change rule:** no breaking change to a `webex.authorization` method/property or emitted event without a major version bump and a consumer transition note.
- **Deprecation:** mark deprecated surfaces in the owning module spec and package README; keep for at least one minor release with a documented replacement.

## Maintenance

- When a public surface is added/changed/removed, update this catalog, the owning module spec's `Public Surface`, and `.sdd/manifest.json` in the same change.
- Cross-reference: domain terms → `GLOSSARY.md`; security controls → `SECURITY.md`.
