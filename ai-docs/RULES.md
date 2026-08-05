<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: rules@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Rules — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry, carries the critical rules) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; per-language detail in `rules/<language>/`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> These rules are checkable. Every MUST rule records its source requirement/risk, verification path,
> severity, and owner. Name the tool where one enforces a rule; say "review only" plus why otherwise.

> Assess-only note: rules below are extracted from committed evidence (ESLint/Prettier config, the
> `@webex/webex-core` code, and the routed architecture doc). Unresolved items are marked
> `[NEEDS HUMAN INPUT]` rather than filled with generic best practice.

## Coverage Map (which docs/specs to trust)
| Module | Manifest coverage state | What it means here |
|---|---|---|
| `packages/@webex/webex-core/` | Partial | Spec is a useful hint migrated from the routed doc; cross-check code before relying on it. |
| all other `packages/*` | Untracked (not enrolled) | No canonical spec; code is the source of truth. |

## Autonomy & Ask-First
- **May proceed:** low-risk changes that don't touch the public export surface, interceptor ordering, credentials, or contracts.
- **Ask first / plan + confirm:** changes to `@webex/webex-core` public exports, the interceptor chain, credential normalization, storage adapters, or any published package's semver surface.
- **Never without explicit human approval:** publishing to the npm registry, deleting the routed legacy doc, overwriting an existing spec in place.

## Naming
- Packages are scoped `@webex/<name>`; internal plugins are `@webex/internal-plugin-*`, public plugins `@webex/plugin-*` (root `package.json` workspaces).
- Interceptor classes end in `Interceptor` and expose a static `create` (`packages/@webex/webex-core/src/webex-core.js`).
- Plugins extend `WebexPlugin` and declare a `namespace` (e.g. `People`) used for config and event prefixes.

## Logging
- Use the logger plugin / logger interceptors; verbose network logging is env-gated
  (`ENABLE_NETWORK_LOGGING`). Never log tokens, credentials, or full auth headers (see `SECURITY.md`).

## Error Handling
- HTTP errors surface as `WebexHttpError` via `HttpStatusInterceptor`
  (`packages/@webex/webex-core/src/webex-core.js`). 401s are handled by `AuthInterceptor`
  (refresh + replay), not swallowed. Do not leak tokens in error messages.

## Imports / Dependencies
- Feature packages depend on `@webex/webex-core`; core must not depend on feature plugins (layering).
- New dependencies go through the workspace's dependency config; browser-incompatible modules must have
  a `browser` shim in the root `package.json`.

## Testing
- Unit tests run under Jest per package (`test:unit`); integration/browser under Mocha/Karma.
  Add positive and negative cases for changed behavior. Coverage bar: `[NEEDS HUMAN INPUT]` — no
  enforced repo threshold was evidenced.

## Security
- Treat all credentials/tokens as secrets; validate the bearer token via `bearerValidator`; enforce
  HTTPS to Webex services. See `SECURITY.md` for the full posture.

## Spec-Currency & Drift Thresholds
- Update the spec/docs in the SAME change as the code (spec-currency).
- Drift thresholds: `[NEEDS HUMAN INPUT]` — no repo coverage-policy file was evidenced; apply the
  default manifest coverage-status definitions until a policy is set.

## Secrets Policy
- No hardcoded secrets/tokens/keys/connection strings — ever. Webex credentials come from the consuming
  application/environment at init; never commit or log them.

<!-- Include if: the repo is concurrent/async/reactive -->
## Concurrency & Async
- The request pipeline and event system are async/event-driven. Handlers must not block; child `change`
  events bubble to the parent and may fire repeatedly — keep listeners idempotent
  (`packages/@webex/webex-core/src/webex-core.js`).

## Maintenance
- Add a rule when a review correction recurs; remove it when a lint rule starts enforcing it.
- Cross-reference: patterns → `patterns/`; per-language → `rules/<language>/`.
