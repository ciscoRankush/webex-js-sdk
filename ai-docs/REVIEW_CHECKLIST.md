<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: review-checklist@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Review-Check Catalog — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc at Review & Merge.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Each finding records: severity (Blocking / Important / Medium / Minor), check id, file path, what's wrong, why it matters, a concrete fix. Any Blocking finding fails the gate.

## Core checks (always run)

| # | Check | What it verifies | Severity if it fails |
|---|---|---|---|
| C1 | Spec-currency + WHAT/WHY | Module spec/docs changed in the same change as code; every requirement states WHAT and WHY | Blocking |
| C2 | Contract correctness | `webex.authorization` method/property/event delta is real and complete; no undocumented breaking change | Blocking |
| C3 | Code-vs-spec match | Signatures, flow selection, and event names in the spec match `src/authorization.js` | Blocking |
| C4 | Test adequacy | Each behavior has a positive AND negative unit case (e.g. CSRF match/mismatch); coverage meets the bar | Important |
| C5 | Error handling + input validation | OAuth errors mapped via `grantErrors`; required inputs (`code`) validated; failure paths not swallowed | Important |
| C6 | Security baseline | CSRF/PKCE/URL-cleanup preserved; no secrets/tokens logged; client secret kept out of public flows (per `SECURITY.md`) | Blocking |

## Coverage-conditional checks (run by the touched module's manifest coverage state)

| # | Check | When it applies | What it verifies | Severity |
|---|---|---|---|---|
| K1 | Regression guard | Modifying any of these Untracked modules | A characterization baseline exists (`test/unit/spec/authorization.js`); invariants the change claims NOT to alter still hold | Blocking |
| K2 | Grounding | Untracked module | Claims cite real code (file path), not memory; uncovered surfaces flagged `[NEEDS HUMAN INPUT]` | Important |
| K3 | Drift threshold | Any tracked module | Module drift within its status threshold (see `RULES.md`) | Important |
| K4 | Coverage-state accuracy | Coverage-state change proposed | Recorded manifest coverage state matches evidence; promotion rules honored | Medium |

## Cross-cutting checks (apply at higher risk / autonomy)

| # | Check | What it verifies | Severity |
|---|---|---|---|
| X1 | Cross-model review | The artifact was validated by a different runtime than the one that generated it (generator ≠ validator) | Blocking when required |
| X2 | Observability | Logging adequate for the change; no tokens/secrets/PII logged | Medium |
| X3 | Rollout safety | `clientType`/flow default is safe; no accidental exposure of confidential-only paths in public bundles | Important |

## How the set is selected

1. Always run the 6 core checks.
2. Add the coverage-conditional checks whose "when it applies" matches the touched modules' manifest coverage state (all four modules are currently Untracked → K1/K2 apply).
3. Add the cross-cutting checks when the change is high-risk (any security-control or public-surface change).

## Output

- A compliance matrix + severity-sorted findings + a verdict (Pass / Pass-with-warnings / Blocked). Draft only; a human posts.
