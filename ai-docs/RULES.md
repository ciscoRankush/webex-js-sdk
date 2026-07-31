<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: rules@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Rules — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry, carries the critical rules) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> These rules are checkable. Every MUST rule records its source requirement/risk and verification path. Rules below are extracted from the real authorization sources; defer to eslint where it already enforces.

## Coverage Map (which docs/specs to trust)

| Module | Manifest coverage state | What it means here |
|---|---|---|
| `plugin-authorization/` | Untracked | Code is source of truth; spec is a draft assess-only migration. |
| `plugin-authorization-browser/` | Untracked | Cross-check code; spec migrated from OAuth guide + README, not yet coverage-measured. |
| `plugin-authorization-node/` | Untracked | Cross-check code. |
| `plugin-authorization-browser-first-party/` | Untracked | Cross-check code; hardened internal flows. |

## Autonomy & Ask-First

- **May proceed:** doc/comment fixes; adding tests that pin current behavior.
- **Ask first / plan + confirm:** any change to a public `webex.authorization` method/property, OAuth flow selection, or emitted event.
- **Never without explicit human approval:** removing/weakening CSRF, PKCE, or URL-cleanup controls; publishing packages; deleting migrated source docs.

## Naming

- Plugins register under the `authorization` namespace via `registerPlugin('authorization', Authorization, {config, proxies})` (`src/index.js`). Keep the namespace stable.
- OAuth params use snake_case (`response_type`, `grant_type`, `code_verifier`, `redirect_uri`) to match the wire format; `eslint camelcase` is intentionally disabled in these files.
- Event names live on the exported `Events` object; `eventType` strings are the stable contract (`getUserCodeSuccess`, `authorizationPending`, …).

## Logging

- Use `this.logger.info` for flow milestones and `this.logger.warn` for recoverable failures (e.g. failed initial code grant). Never log tokens, secrets, `code`, `code_verifier`, CSRF tokens, or raw email. See `SECURITY.md`.

## Error Handling

- Map OAuth 400 responses to typed errors via `grantErrors.select(res.body.error)` and reject with the constructed error; propagate non-400 responses unchanged.
- Validate required inputs early: `requestAuthorizationCodeGrant` rejects when `options.code` is missing.
- For device polling, emit `authorizationFailure` on terminal errors and stop polling; do not swallow.

## Imports / Dependencies

- Depend on workspace packages via `workspace:*`; do not pin cross-package versions manually (version-sync is handled by `@webex/package-tools`).
- Browser/public packages must not import Node-only secret-handling paths into the public bundle.

## Testing

- Each behavior gets a unit test asserting a positive AND a negative case (e.g. CSRF match vs mismatch; `code` present vs missing). Unit tests live under `test/unit/spec/authorization.js` per package and run via `yarn test:unit` (jest through `webex-legacy-tools`).

## Security

- Preserve CSRF state verification, PKCE (S256) single-use verifier, and post-redirect URL cleanup. Keep the client secret out of public browser flows. See `SECURITY.md` for the full posture.

## Spec-Currency & Drift Thresholds

- Update the module spec/docs in the SAME change as the code (spec-currency).
- Drift thresholds follow the manifest coverage state (Untracked → code is source of truth; promote before tightening).

## Secrets Policy

- No hardcoded secrets/tokens/keys — ever. `client_secret` comes from consumer SDK config and is used only server-side/confidential; never logged.

## Concurrency & Async

- `@whileInFlight('isAuthorizing')` and `@oneFlight` guard overlapping grant requests — keep them on the relevant methods.
- Device polling must remain idempotent against late responses via the `pollingId`/`currentPollingId` guard and must clear both timers on cancel.

## Maintenance

- Add a rule when a review correction recurs; remove it when an eslint rule starts enforcing it.
- Cross-reference: patterns → `patterns/` (not yet populated in assess-only).
