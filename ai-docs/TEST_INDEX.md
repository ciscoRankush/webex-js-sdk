<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: test-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Test Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). This doc is the repo-wide map of the test surface.
> Context-efficiency: this is an INDEX, not a case list. It links to where cases live — it does not duplicate them.

## Test Surface
| Tier | Command (role) | Test directory | Framework | External deps |
|---|---|---|---|---|
| Unit | `yarn test:unit` (unit-test) | `packages/*/test/unit/spec` | mocha | none |
| Integration | `yarn test:integration` (integration-test) | `packages/*/test/integration/spec` | karma | browser: chrome |
| E2E / System | `yarn test:e2e` (e2e) | `cc_playwright` | playwright | browser: chromium |

Single-package/file targeting: `yarn workspace @webex/<plugin> test:unit --targets <path-relative-to-spec>`
(see `GETTING_STARTED.md`).

## Where the Cases Live
- **Unit test cases** → each module's spec, "Test-Case Strategy (module)" section (see `SPEC_INDEX.md` for the module registry).
- **Integration / E2E / QA cases** → the owning package's `test/integration/spec` and the `cc_playwright` suite.

## Coverage / Quality Gate
- Minimum: not resolved · Measures: `[NEEDS HUMAN INPUT]` · Applies to: `[NEEDS HUMAN INPUT]` · Enforced in: no committed in-build enforced gate was found during onboarding (`karma-coverage` is present but no enforced `min`). Recorded in the manifest as `quality_gates.code_coverage.origin = unknown`; resolve with the repo owner before relying on a coverage gate.

## QA Dependencies & Environments
- Integration/browser tests require a browser (Karma/Playwright); test users/servers are provided by the `@webex/test-helper-*` and `@webex/test-users` packages. No external manual-QA tracker recorded during onboarding.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Machine source of truth: `.sdd/manifest.json` (`commands`, `tests`, `quality_gates`).
