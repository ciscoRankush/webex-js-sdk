<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: test-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Test Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). This doc is the repo-wide map of the test surface.
> Context-efficiency: this is an INDEX, not a case list. It links to where cases live — it does not duplicate them.

## Test Surface
| Tier | Command (role) | Test directory | Framework | External deps |
|---|---|---|---|---|
| Unit | `yarn test:unit` | `packages` (per-package `src`) | Jest | none |
| Integration | `yarn test:integration` | `packages` (per-package) | Karma / Mocha | browser (Karma launchers) |

## Where the Cases Live
- **Unit test cases** → each module's spec, "Test-Case Strategy (module)" section (see `SPEC_INDEX.md`).
- **Integration / browser cases** → per-package test setup (`webex-legacy-tools test`).

<!-- Include if: the repo enforces a coverage / static-analysis / lint gate -->
## Coverage / Quality Gate
- Minimum: `[NEEDS HUMAN INPUT]` · Measures: `[NEEDS HUMAN INPUT]` · Applies to: `[NEEDS HUMAN INPUT]` · Enforced in: `[NEEDS HUMAN INPUT]`.
  No committed in-build coverage gate was found; the manifest records `quality_gates.code_coverage.origin: unknown`.

## QA Dependencies & Environments
- Integration/browser tests use Karma with browser launchers (chrome/firefox/etc.) and, for CI, BrowserStack/Sauce services (`package.json` devDependencies). Manual-QA tracker location: `[NEEDS HUMAN INPUT]`.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Machine source of truth: `.sdd/manifest.json` (`commands`, `tests`, `quality_gates`).
