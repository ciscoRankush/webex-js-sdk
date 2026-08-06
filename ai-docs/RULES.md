<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: rules@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Rules — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry, carries the critical rules) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md).
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> These rules are checkable. Rules below are extracted from the repo's committed dev guide, test-writing
> conventions, and build config — not generic best practice. Defer to the linter where it already enforces.

## Coverage Map (which docs/specs to trust)
| Module | Manifest coverage state | What it means here |
|---|---|---|
| All 93 workspace modules | `Partial` (unmeasured pending host coverage-review) | Spec is a hint; cross-check code. Migrated `contact-center`/`calling` specs carry richer source content but are not yet coverage-measured. |

> Authoritative per-module state lives in `.sdd/manifest.json`; this table mirrors the onboarding baseline.

## Autonomy & Ask-First
- **May proceed:** low-risk changes scoped to a single plugin that don't touch public exports, contracts, auth, or encryption.
- **Ask first / plan + confirm:** exported package surface changes, cross-plugin refactors, auth/encryption changes, anything that affects semver.
- **Never without explicit human approval:** publishing/release, deleting data, pushing, or posting to trackers/PRs.

## Naming
- Package names are scoped: public `@webex/plugin-*`, internal `@webex/internal-plugin-*`, helpers/config/tools under their folders (evidence: `package.json` `workspaces`, `packages/@webex/*`).
- Plugins expose a `namespace` and extend `WebexPlugin`; events use `change:<namespace>` propagation (evidence: migrated `webex-plugin-architecture.md`).

## Logging
- Use `@webex/plugin-logger` per-plugin loggers; never log access tokens, credentials, or PII (see `SECURITY.md`).

## Error Handling
- Network 401s are handled centrally by the `AuthInterceptor` (refresh + replay) in `webex-core`; do not re-implement re-auth per plugin. Surface caller-relevant errors; do not swallow.

## Imports / Dependencies
- Plugins depend on `@webex/webex-core`; keep workspace-internal deps version-synced via `@webex/package-tools`. New third-party deps need lead approval and must respect the browser/Node dual-target (`package.json` `browser` field).

## Testing
- Match existing test style: sinon for mocks/stubs; `assert` from `@webex/test-helper-chai`; prefer `assert.calledOnceWithExactly` over separate `calledOnce`/`calledWith`; use `sinon.useFakeTimers()` to control time.
- Parametrize when there are more than 3 similar cases; reuse existing helper methods instead of duplicating test code (evidence: root dev guide test-writing guidelines).
- Add positive AND negative cases for changed behavior.

## Security
- Validate inputs at the boundary; keep auth on the interceptor path; no secrets in code (see `SECURITY.md`).

## Spec-Currency & Drift Thresholds
- Update the module spec/docs in the SAME change as the code (spec-currency).
- Drift thresholds follow the manifest coverage-status definitions (Specced ≥80%/<5% drift; Partial 40–80%; Untracked <40%).

## Secrets Policy
- No hardcoded secrets/tokens/keys — ever. Access tokens come from the developer's Webex credentials at runtime; never log or commit them.

## Concurrency & Async
<!-- Kept: the repo is concurrent/async/reactive (websocket transports, event bubbling) -->
- Mercury/LLM/Voicea websocket handlers and event propagation are async; do not block on the request pipeline. Treat realtime event delivery as connection-dependent and handle reconnect.

## Maintenance
- Add a rule when a review correction recurs; remove it when a lint rule starts enforcing it.
