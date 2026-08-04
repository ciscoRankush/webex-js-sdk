<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: rules@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Rules — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry, carries the critical rules) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md).
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> These rules are checkable. Every MUST rule records its source requirement/risk, verification path,
> severity, and owner. Name the tool where one enforces a rule; say "review only" plus why otherwise.

## Coverage Map (which docs/specs to trust)
| Module | Manifest coverage state | What it means here |
|---|---|---|
| `packages/@webex/webex-core/` | Partial | Spec is a hint migrated from `webex-plugin-architecture.md`; cross-check against `src/` before relying on it |
| all other `packages/**` | Untracked | No spec — code is the source of truth; do not assume documented behavior |

## Autonomy & Ask-First
- **May proceed:** low-risk changes that don't touch public exports, credentials, or the interceptor pipeline.
- **Ask first / plan + confirm:** changes to plugin registration, the HTTP interceptor chain, credential/auth handling, storage adapters, or any published export.
- **Never without explicit human approval:** publishing to npm (`deploy:npm`), pushing, or deleting the routed source doc.

## Naming
- Packages are scoped `@webex/*`; internal plugins use the `internal-plugin-` prefix, public ones `plugin-` (`packages/@webex/`). Follow the existing prefix convention; do not invent a new package-naming scheme.
- Interceptors are PascalCase classes suffixed `Interceptor` (`packages/@webex/webex-core/src/interceptors/`).

## Logging
- Use the plugin logger (`@webex/plugin-logger`) rather than `console`. Network logging is gated behind `ENABLE_NETWORK_LOGGING` / `ENABLE_VERBOSE_NETWORK_LOGGING` (`webex-core.js`). Never log tokens or credentials (see `SECURITY.md`).

## Error Handling
- HTTP failures surface as `WebexHttpError` (`packages/@webex/webex-core/src/lib/webex-http-error.js`); 401s are handled by the `AuthInterceptor` with bounded refresh/replay. Do not swallow request errors.

## Imports / Dependencies
- Intra-repo dependencies use the `workspace:*` protocol; do not import another package by relative path across package boundaries. New third-party deps require review.

## Testing
- Each package defines `test:unit` (Jest), `test:integration` (Mocha), and browser tests (Karma). Add positive and negative cases; keep unit-test detail in the module spec's Test-Case Strategy section.

## Security
- Validate/normalize credentials at the boundary (`WebexCore` constructor + `bearerValidator`); never hardcode secrets. See `SECURITY.md` for the full posture.

## Spec-Currency & Drift Thresholds
- Update the spec/docs in the SAME change as the code (spec-currency). For `webex-core` (Partial), keep drift within the Partial threshold and cross-check code.
- Repo-wide drift thresholds beyond the manifest defaults: `[NEEDS HUMAN INPUT]`.

## Secrets Policy
- No hardcoded secrets/tokens/keys/connection strings — ever. OAuth tokens are supplied at runtime via credentials; never log or commit them.

<!-- Include if: the repo is concurrent/async/reactive -->
## Concurrency & Async
- The request pipeline and event system are async/promise-based. Interceptors must not block; event handlers should tolerate re-delivery. Preserve ordering guarantees only where the pipeline already provides them.

## Maintenance
- Add a rule when a review correction recurs; remove it when a lint rule starts enforcing it.
- These rules were extracted from `webex-core` sources and the routed architecture doc; expand as more modules are onboarded.
