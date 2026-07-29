# Spec Index — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry). This file is the router (generated at `ai-docs/SPEC_INDEX.md`); system overview in [`ARCHITECTURE.md`](ARCHITECTURE.md). Load `AGENTS.md` + this file first; pull every other doc on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; route to the minimum needed per task.

> **SDD status: assess-only draft.** Coverage state below is **non-authoritative** and gates nothing.
> **Source of truth:** `.sdd/manifest.json` (this file mirrors it for humans). Code overrides any spec.

## Coverage legend
- **Partial** — a canonical module spec (`<module-path>/ai-docs/<module-name>-spec.md`) exists but is not yet full public-surface coverage. It was produced either by migrating a routed existing design/spec doc **by meaning**, or by a **code-derived backfill** from the module's public surface and tests. Treat the spec as a hint and cross-check code.
- **Untracked** — source (and usually tests) present, but no canonical SDD spec or routed prior spec yet.

There are **79 workspace modules**. 11 are `Partial` (8 from routed-source migration, plus `@webex/common`, `@webex/common-evented`, and `@webex/common-timers` from code-derived backfills); the rest are `Untracked`.
No module is `Specced` yet — full public-surface coverage is a rigorous-mode step. The 11 Partial module specs already exist at the spec paths below; `Untracked` modules have no canonical spec yet (generate one via `doc-backfill` in rigorous mode).

## Module Registry
| Module | Responsibility | Manifest coverage state | Canonical spec (Untracked = none yet) |
|---|---|---|---|
| `packages/@webex/common/` | Core runtime / shared utility | Partial | `packages/@webex/common/ai-docs/common-spec.md` |
| `packages/@webex/common-evented/` | Core runtime / shared utility | Partial | `packages/@webex/common-evented/ai-docs/common-evented-spec.md` |
| `packages/@webex/common-timers/` | Core runtime / shared utility | Partial | `packages/@webex/common-timers/ai-docs/common-timers-spec.md` |
| `packages/@webex/contact-center/` | Entry bundle / standalone SDK | Partial | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| `packages/@webex/helper-html/` | Core runtime / shared utility | Untracked | no canonical spec (untracked) |
| `packages/@webex/helper-image/` | Core runtime / shared utility | Untracked | no canonical spec (untracked) |
| `packages/@webex/http-core/` | Core runtime / shared utility | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-ai-assistant/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-avatar/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-board/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-calendar/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-conversation/` | Internal plugin | Partial | `packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md` |
| `packages/@webex/internal-plugin-device/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-dss/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-ediscovery/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-encryption/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-feature/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-flag/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-llm/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-locus/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-lyra/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-mercury/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-metrics/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-presence/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-scheduler/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-search/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-support/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-task/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-team/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-user/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-voicea/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/internal-plugin-wdm/` | Internal plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/jsdoctrinetest/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/media-helpers/` | Core runtime / shared utility | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-attachment-actions/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-authorization/` | Public capability plugin | Partial | `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` |
| `packages/@webex/plugin-authorization-browser/` | Public capability plugin | Partial | `packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md` |
| `packages/@webex/plugin-authorization-browser-first-party/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-authorization-node/` | Public capability plugin | Partial | `packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md` |
| `packages/@webex/plugin-device-manager/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-encryption/` | Public capability plugin | Partial | `packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md` |
| `packages/@webex/plugin-logger/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-meetings/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-memberships/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-messages/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-people/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-presence/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-rooms/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-team-memberships/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-teams/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/plugin-webhooks/` | Public capability plugin | Untracked | no canonical spec (untracked) |
| `packages/@webex/recipe-private-web-client/` | Entry bundle / standalone SDK | Untracked | no canonical spec (untracked) |
| `packages/@webex/storage-adapter-local-forage/` | Storage adapter | Untracked | no canonical spec (untracked) |
| `packages/@webex/storage-adapter-local-storage/` | Storage adapter | Untracked | no canonical spec (untracked) |
| `packages/@webex/storage-adapter-session-storage/` | Storage adapter | Untracked | no canonical spec (untracked) |
| `packages/@webex/storage-adapter-spec/` | Storage adapter | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-appid/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-automation/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-chai/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-file/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-make-local-url/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-mocha/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-mock-web-socket/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-mock-webex/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-refresh-callback/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-retry/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-server/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-helper-test-users/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-users/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/test-webex-node/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/webex-core/` | Core runtime / shared utility | Partial | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` |
| `packages/@webex/webex-server/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/@webex/webrtc/` | Core runtime / shared utility | Untracked | no canonical spec (untracked) |
| `packages/@webex/xunit-with-logs/` | Test infrastructure / helper | Untracked | no canonical spec (untracked) |
| `packages/webex/` | Entry bundle / standalone SDK | Untracked | no canonical spec (untracked) |
| `packages/webex-node/` | Entry bundle / standalone SDK | Untracked | no canonical spec (untracked) |
| `packages/calling/` | Entry bundle / standalone SDK | Partial | `packages/calling/ai-docs/calling-spec.md` |
| `packages/byods/` | Entry bundle / standalone SDK | Untracked | no canonical spec (untracked) |
| `packages/byods-demo-server/` | Entry bundle / standalone SDK | Untracked | no canonical spec (untracked) |

## Task Routing
| If the task is… | Load |
|---|---|
| Understanding the system | `ARCHITECTURE.md` |
| Working in a Partial module | that module's canonical spec (already generated) + the module source (cross-check) |
| Working in an Untracked module | the module source (source of truth); generate a spec via `doc-backfill` in rigorous mode |
| A public-API / contract change | `CONTRACTS.md` + the generated API reference it links + `ARCHITECTURE.md` interaction section |
| Auth / OAuth work | routed OAuth guides (see manifest `spec_sources`) + `SECURITY.md` |
| Updating docs after a code change | affected module spec(s) + relevant standing indexes/contracts |
| Migrating existing specs | `.sdd/manifest.json` source routes + `.generated/sdd/spec-source-policy/decision.md` + source-fidelity report |

## Spec-source routing (migrate-existing)
Routed existing specs (source-material, `retain`) → owning module. Full list + notes in
`.sdd/manifest.json` `spec_sources` and `.generated/sdd/spec-source-policy/decision.md`:

| Source doc | Owning module |
|---|---|
| `webex-plugin-architecture.md` | `packages/@webex/webex-core/` (also informs `ARCHITECTURE.md`) |
| `.../internal-plugin-conversation/src/activity-threading.md` | internal-plugin-conversation |
| `.../plugin-authorization/OAUTH-FLOW-GUIDE.md` | plugin-authorization |
| `.../plugin-authorization-browser/BROWSER-OAUTH-FLOW-GUIDE.md` | plugin-authorization-browser |
| `.../plugin-authorization-node/NODE-OAUTH-FLOW-GUIDE.md` | plugin-authorization-node |
| `.../calling/usm sdk flow.md` | calling |
| `.../plugin-encryption/developer-quickstart.md` | plugin-encryption |
| `.../contact-center/typedoc.md` | contact-center |

## Incident History
No repo-tracked incident/RCA records exist at onboarding (assess-only). Add a one-line reference row per
incident here, linking the full RCA in the tracker/wiki — do not paste RCA bodies into this file.

| INC id | Date | Module | One-line | Link |
|---|---|---|---|---|
| _none tracked_ | — | — | — | — |

## Spec Registry
| Doc | Location | Purpose |
|---|---|---|
| Architecture | `ai-docs/ARCHITECTURE.md` | System components, interactions, flows |
| Contracts | `ai-docs/CONTRACTS.md` | Public API index + links to generated API reference |
| Rules | `ai-docs/RULES.md` | Enforceable do/don't beyond AGENTS.md critical rules |
| Security | `ai-docs/SECURITY.md` | Trust boundaries, authn/authz, token/secret handling |
| Manifest (authoritative) | `.sdd/manifest.json` | Topology, commands, modules, coverage, source routing |
| Spec-source decision | `.generated/sdd/spec-source-policy/decision.md` | migrate-existing policy record |

## Open confirmations ([NEEDS HUMAN INPUT])
1. Module map (79 modules) — confirm the module set before rigorous-mode spec/manifest writes (CRITICAL).
2. Repo category — is commit/PR history trustworthy evidence for WHY behavior exists (affects `doc-backfill`)?
3. SDD layout — confirm default (root `AGENTS.md`, standing docs in `ai-docs/`, source-local module specs).
4. Per-source retention — confirm `retain` vs `delete-after-validation` for each routed source path.
