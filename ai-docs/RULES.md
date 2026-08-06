<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: rules@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->

# Rules — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry, carries the critical rules) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc; per-language detail in `rules/<language>/`.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> These rules are checkable. Every MUST rule records its source requirement/risk, verification path,
> severity, and owner. Name the tool where one enforces a rule; say "review only" plus why otherwise.

## Coverage Map (which docs/specs to trust)
| Module | Manifest coverage state | What it means here |
|---|---|---|
| All 93 workspace modules | `Partial` (assess-only baseline) | Spec is a hint; cross-check code. Coverage not yet measured — the host coverage-review phase sets real scores. |
| `packages/@webex/contact-center`, `packages/calling` | `Partial` (migrated spec trees) | Rich pre-existing ai-docs being migrated by meaning; code/tests remain the behavioral referee. |

Authoritative per-module state is `.sdd/manifest.json`; `SPEC_INDEX.md` mirrors it.

## Autonomy & Ask-First
- **May proceed:** low-risk changes scoped to one plugin that don't touch a public export, credential, or encryption path.
- **Ask first / plan + confirm:** changes to a public plugin's exported surface, transport/mercury behavior, auth flows, or anything crossing package boundaries.
- **Never without explicit human approval:** publishing to the npm registry, deleting data, force-push, or posting to trackers/PRs.

## Naming
- Extracted convention: the codebase mixes named state constants (e.g. `LOCUS.STATE.*`, `MEETING_STATE.STATES.*`) with inline string literals (`'LEFT'`, `'MOVED'`). When adding logic, prefer the named constant but search for both forms (root `AGENTS.md`).
- Packages are scoped `@webex/<name>`; internal (non-published) capability plugins use the `internal-plugin-` prefix, public ones use `plugin-` (root `package.json`, `ARCHITECTURE.md`).

## Logging
- Use `plugin-logger` for structured logging. Never log OAuth tokens, KMS key material, or decrypted message/content plaintext (see `SECURITY.md`).

## Error Handling
- Follow each plugin's existing error idiom; do not swallow failures or leak internal details to SDK consumers. Failure/edge paths must be handled, not silently dropped.

## Imports / Dependencies
- Public `plugin-*` packages must not import another package's internals directly — go through `webex-core`/documented exports. New third-party deps need lead approval and must respect the workspace resolution/version-sync rules.

## Testing
- Match the existing test style of the file/package: `sinon` for mocks/stubs; `assert` from `@webex/test-helper-chai`; prefer `assert.calledOnceWithExactly` over chained `calledOnce`/`calledWith`; `sinon.useFakeTimers()` for time control; parametrize when there are more than 3 similar cases; reuse or add shared helper methods rather than duplicating (root `AGENTS.md`).
- Add a positive and a negative case per behavior. Tests live under each package's `test/unit/spec/` (and `test/integration/spec/`); enforced coverage bar is `[NEEDS HUMAN INPUT]` — no committed in-build coverage gate was found.

## Security
- No hardcoded secrets/tokens/keys. Validate untrusted input at plugin boundaries; keep auth and encryption on their owning plugins. Full posture: `SECURITY.md`.

## Spec-Currency & Drift Thresholds
- Update the spec/docs in the SAME change as the code (spec-currency).
- Drift thresholds: mirror the repo's manifest coverage policy; `Partial` modules require code cross-check until the host coverage-review sets measured scores.

## Secrets Policy
- No hardcoded secrets/tokens/keys/connection strings — ever. OAuth credentials are supplied at runtime and held in `webex-core`; never log or commit them.

## Concurrency & Async
<!-- Include-if the repo is concurrent/async/reactive -->
- The SDK is event-driven: Mercury delivers server-pushed events and plugins emit change events. Event handlers should not assume single delivery; keep them resilient to reconnection and re-delivery. Do not block on long-running work inside an event handler.

## Maintenance
- Add a rule when a review correction recurs; remove it when a lint rule starts enforcing it.
- Cross-reference: patterns → `patterns/`; per-language → `rules/<language>/`.
