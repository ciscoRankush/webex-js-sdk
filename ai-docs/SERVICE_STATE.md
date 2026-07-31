<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: service-state@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Service State (living) — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Read this FIRST before adding a surface; stable contracts in `CONTRACTS.md`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Source of truth for "does X already exist?" Keep current in the same change that adds/removes a surface.
> These are libraries, not a running service: there are no served endpoints, owned data stores, rate limits, or SLOs. The live surface is the SDK export API, emitted events, and consumed external dependencies.

## Current Events

| Event / topic | Direction | Producer/consumer | Payload ref |
|---|---|---|---|
| `login` | publish | `plugin-authorization-browser-first-party` → UI subscribers | `{eventType: initiateLogin\|redirectToLoginUrl, data}` |
| `qRCodeLogin` | publish | `plugin-authorization-browser-first-party` → UI subscribers | `{eventType, userData?, data?}` (see `CONTRACTS.md`) |

## External Dependencies

| Dependency | Used for | Timeout / retry | Circuit breaker / fallback |
|---|---|---|---|
| Webex IdBroker | authorize/token OAuth endpoints | none set by these packages | 400 → typed `grantErrors`; other errors propagate |
| hydra (`jwt/login`) | JWT → access token exchange | none set | promise rejection propagates |
| oauth-helper | device authorize/token (QR) | server-provided poll `interval`; overall `expires_in` timeout | `slow_down` doubles interval once; `428` → keep polling; terminal error → `authorizationFailure` |
| `webex.credentials` | token storage | n/a | required dependency |

## Feature Flags (current)

| Flag | Gates | Current default | Owner | Safe to remove when |
|---|---|---|---|---|
| `clientType` (config, not a flag) | OAuth flow selection (confidential vs public) | `'public'` | SDK config | n/a — stable config knob |

## Compliance / Certifications

- Tokens and secrets must never be logged or persisted in plaintext by these packages; PII (email) is hashed (SHA256) before propagation in the first-party preauth path. `[NEEDS HUMAN INPUT]` for any formal certification obligations.

## Maintenance

- Update the relevant row in the same change that adds/changes/removes an exported method, event, or external dependency.
- Cross-reference: stable contracts → `CONTRACTS.md`; security posture → `SECURITY.md`.
