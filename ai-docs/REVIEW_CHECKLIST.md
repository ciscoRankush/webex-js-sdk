<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: review-checklist@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->
# Review-Check Catalog — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc at Review & Merge.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

> Each finding records: severity (Blocking / Important / Medium / Minor), check id, file path, what's wrong,
> why it matters, a concrete fix. Any Blocking finding fails the gate.

## Core checks (always run)
| # | Check | What it verifies | Severity if it fails |
|---|---|---|---|
| C1 | Spec-currency + WHAT/WHY | Spec/docs changed in the same change as code; every requirement states WHAT and WHY | Blocking |
| C2 | Contract correctness | Provides/Requires delta is real and complete; no undocumented breaking change to an exported package surface | Blocking |
| C3 | Code-vs-spec match | Signatures, data-flow, and architecture claims match the actual code (file path) | Blocking |
| C4 | Test adequacy | Each acceptance criterion has a positive AND negative test; changed-line coverage meets the bar | Important |
| C5 | Error handling + input validation | Untrusted input validated at boundaries; failure/edge paths handled, not swallowed | Important |
| C6 | Security baseline | No hardcoded secrets/tokens; auth on the interceptor path; logging rules respected (per `SECURITY.md`) | Blocking |

## Coverage-conditional checks (run by the touched module's manifest coverage state)
| # | Check | When it applies | What it verifies | Severity |
|---|---|---|---|---|
| K1 | Regression guard | Modifying a weakly covered module, or any MODIFIED/REMOVED requirement | A characterization baseline exists; invariants hold (positive + negative) | Blocking |
| K2 | Grounding | Weakly covered module (all modules are `Partial` until first coverage-review) | Claims cite real code (file path); uncovered surfaces flagged `[NEEDS HUMAN INPUT]` | Important |
| K3 | Drift threshold | Any tracked module | Module drift within its status threshold (see `RULES.md`) | Important |
| K4 | Coverage-state accuracy | Coverage-state change proposed | Recorded manifest coverage state matches evidence | Medium |

## Cross-cutting checks (apply at higher risk / autonomy)
| # | Check | What it verifies | Severity |
|---|---|---|---|
| X1 | Cross-model review | Artifact validated by a different runtime than generated it (generator ≠ validator; manifest `minimum_independence: different-runtime`) | Blocking when required |
| X2 | Observability | Logs/metrics adequate; nothing sensitive logged | Medium |
| X3 | Rollout safety | Feature-flag default safe; rollback path exists | Important |

## How the set is selected
1. Always run the 6 core checks.
2. Add coverage-conditional checks matching the touched modules' manifest coverage state (currently all `Partial`).
3. Add cross-cutting checks for high-risk or higher-autonomy changes.

## Output
- A compliance matrix + severity-sorted findings + a verdict (Pass / Pass-with-warnings / Blocked). Draft only; a human posts.
