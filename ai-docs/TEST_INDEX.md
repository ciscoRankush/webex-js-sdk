<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: test-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Test Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). This doc is the repo-wide map of the test surface.
> Context-efficiency: this is an INDEX, not a case list. It links to where cases live — it does not duplicate them.

## Test Surface
| Tier | Command (role) | Test directory | Framework | External deps |
|---|---|---|---|---|
| Unit | `yarn test:unit` | per-package `test/unit` (e.g. `packages/@webex/webex-core/test`) | Jest | none |
| Integration | `yarn test:integration` | per-package integration/browser tests | Karma / Mocha + WebdriverIO | browsers (Chrome/Firefox), Webex test credentials |

Source: root `package.json` `scripts` (`test`, `test:unit`, `test:integration`) and
`packages/@webex/webex-core/package.json` (`test:unit` → Jest, `test:integration` → Mocha,
`test:browser` → Karma).

## Where the Cases Live
- **Unit test cases** → each module's spec, "Test-Case Strategy (module)" section (see `SPEC_INDEX.md` for the module registry).
- **Integration / E2E / scale / security / resiliency / QA cases** → the per-feature test strategy at `features/<KEY>/test-strategy.md`.

<!-- Include if: the repo enforces a coverage / static-analysis / lint gate -->
## Coverage / Quality Gate
- `[NEEDS HUMAN INPUT]` — no committed in-build coverage gate (threshold) was evidenced in the root or
  `@webex/webex-core` build config. `karma-coverage` is present as a devDependency but no enforced
  minimum was found. Confirm whether a CI/org coverage gate applies before treating coverage as blocking.

## QA Dependencies & Environments
- Integration/browser tiers depend on real browsers and Webex platform test credentials; these are
  provided by the environment/CI, not committed. No manual-QA tracker location was evidenced.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Machine source of truth: `.sdd/manifest.json` (`commands`, `tests`, `quality_gates`).
