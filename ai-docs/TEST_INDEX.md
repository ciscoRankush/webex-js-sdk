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
| Unit | `yarn test:unit` | `packages/*/test/unit` | Mocha/Karma/Jasmine/Jest (per package) | none |
| Integration | `yarn test:integration` | `packages/*/test/integration` | Karma/Mocha (browser) | test users / local fixture server |
| E2E / System | `yarn test:e2e` | `cc_playwright` and `packages/*/test/e2e` | Playwright / WebdriverIO | browsers/BrowserStack |

Commands mirror root `package.json` `scripts`; per-package runners vary — read the package's own
`test:unit` wiring before assuming a framework.

## Where the Cases Live
- **Unit test cases** → each module's spec, "Test-Case Strategy (module)" section (see `SPEC_INDEX.md` for the module registry).
- **Integration / E2E / QA cases** → per-package `test/integration` and the Playwright suite under `cc_playwright/`.

## Coverage / Quality Gate
<!-- Include-if the repo enforces a coverage/static-analysis/lint gate -->
- Minimum: `[NEEDS HUMAN INPUT]` · Measures: unit-test coverage (karma-coverage/nyc tooling present) · Applies to: `[NEEDS HUMAN INPUT]` · Enforced in: no committed in-build coverage gate was found; the enforced threshold (if any lives in CI/Sonar/org policy) must be confirmed by the repo owner.

## QA Dependencies & Environments
- Integration/e2e rely on provisioned test users (`@webex/test-users`, `test-helper-test-users`) and browser automation (Karma/WebdriverIO/BrowserStack). Manual-QA tracker location: `[NEEDS HUMAN INPUT]`.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Machine source of truth: `.sdd/manifest.json` (`commands`, `tests`, `quality_gates`).
