<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: spec-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Spec Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry). This file is the router (generated at `ai-docs/SPEC_INDEX.md`); system overview in [`ARCHITECTURE.md`](ARCHITECTURE.md). Load `AGENTS.md` + this file first; pull every other doc on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; route to the minimum needed per task.

> AI agent entry point after `AGENTS.md`. Load this once at session start; pull other docs on demand.
> **Source of truth:** `.sdd/manifest.json` (this file mirrors it for humans).

## Module Registry
| Module | Responsibility | Manifest coverage state | Start here |
|---|---|---|---|
| `packages/@webex/webex-core/` | Plugin host, HTTP request pipeline, credentials/auth, storage, config, events | Partial | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |

> Assess-only scope note: only `@webex/webex-core` is tracked with a canonical spec in this bootstrap
> (it is the module the routed `webex-plugin-architecture.md` documents). Other packages in the
> `packages/` tree remain code-first and are not yet enrolled as tracked modules. Enrolling them is a
> deferred decision — see `.generated/sdd/bootstrap-questionnaire.md`.

## Task Routing
| If the task is… | Load |
|---|---|
| Understanding the system | `ARCHITECTURE.md` |
| Working in `@webex/webex-core` | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| A cross-service contract change | `CONTRACTS.md` + `ARCHITECTURE.md` interaction section |
| Running or changing tests | `TEST_INDEX.md` + the affected module spec |
| Updating docs after a code change | affected module specs + relevant standing indexes/contracts |
| Migrating existing specs | manifest source routes + affected module specs + source-fidelity report |

## Incident History
| INC id | Date | Module | One-line | Link |
|---|---|---|---|---|
| `[NEEDS HUMAN INPUT]` | — | — | No incident history was evidenced in the repository | — |

<!-- Include if: the repo is large enough that phase-based on-demand spec loading is worthwhile -->
## Phase-Based Loading Protocol
| Phase | Load |
|---|---|
| Orient | AGENTS.md + this file |
| Specify | relevant module docs and routed source specs |
| Build | the selected module SPEC(s) + patterns/rules |
| Verify | independent validation |

## Spec Registry
| Doc | Location | Purpose |
|---|---|---|
| Patterns | `patterns/` | repo conventions, correct vs incorrect |
| Rules | `RULES.md` + `rules/` | enforceable do/don't beyond AGENTS.md critical rules |
| Glossary | `GLOSSARY.md` | ubiquitous language: term → definition → code location |
| Security | `SECURITY.md` | trust boundaries, authn/authz, secret handling, data classification |
| Contracts | `CONTRACTS.md` | root index of public-surface contracts; details live at owning modules or native contract sources |
| Service state | `SERVICE_STATE.md` | living as-built registry — read first to avoid duplicate/breaking surfaces |
| Test index | `TEST_INDEX.md` | test tiers, canonical commands, locations, frameworks, dependencies, and quality gates |
| Getting started | `GETTING_STARTED.md` | clone/build/run + multi-repo workspace layout |
| Decision records | `adr/` | standing ADRs — why the architecture is the way it is |
| Review catalog | `REVIEW_CHECKLIST.md` | the 6-core + 4-coverage + 3-cross-cutting review checks |
