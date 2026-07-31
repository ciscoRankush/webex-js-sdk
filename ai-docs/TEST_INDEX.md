<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: test-index@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Test Index — `Webex JS SDK (authorization plugin family)`

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). This doc is the repo-wide map of the test surface.
> Context-efficiency: this is an INDEX, not a case list. It links to where cases live — it does not duplicate them.

## Test Surface

| Tier | Command (role) | Test directory | Framework | External deps |
|---|---|---|---|---|
| Unit | `yarn test:unit` (unit-test) | `packages/@webex/plugin-authorization-*/test/unit` | Jest (via `webex-legacy-tools`) | none |
| Integration | `yarn test:integration` (integration-test) | `packages/@webex/plugin-authorization-*/test/integration` | Mocha / Karma (via `webex-legacy-tools`) | Webex test users / browser runners |

## Where the Cases Live

- **Unit test cases** → each module's spec, "Test-Case Strategy (module)" section (see `SPEC_INDEX.md` for the module registry). Concrete files: `packages/@webex/plugin-authorization-browser/test/unit/spec/authorization.js`, `.../plugin-authorization-browser-first-party/test/unit/spec/authorization.js`, and the node package's equivalent.
- **Integration / browser cases** → each package's `test/integration` and browser runners.

## Coverage / Quality Gate

- Minimum: `[NEEDS HUMAN INPUT]` · Measures: `[NEEDS HUMAN INPUT]` · Applies to: `[NEEDS HUMAN INPUT]` · Enforced in: `[NEEDS HUMAN INPUT]`. No committed in-build coverage minimum was found; `karma-coverage` tooling exists but the enforced gate (if any) must be confirmed by the repo owner (manifest `quality_gates.code_coverage.origin = unknown`).

## QA Dependencies & Environments

- Integration/browser tiers rely on Webex test users and browser launchers (karma/webdriver). Manual QA tracker location: `[NEEDS HUMAN INPUT]`.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Machine source of truth: `.sdd/manifest.json` (`commands`, `tests`, `quality_gates`).
