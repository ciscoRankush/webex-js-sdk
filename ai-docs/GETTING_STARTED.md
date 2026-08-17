<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: getting-started@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-06T00:00:00Z
validation_status: not-run
-->

# Getting Started — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc to get a build/test loop running.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

## Prerequisites

### Toolchain
| Tool | Version | Where it's pinned |
|---|---|---|
| Node | `18.x` | `package.json` (`engines.node`) |
| npm | `>=10.5` | `package.json` (`engines.npm`) |
| Yarn | `3.4.1` | `package.json` (`packageManager`) |

> Note: the root `AGENTS.md` prose has referenced Node 22.14 for some workflows while `engines`
> pins `18.x`. The authoritative Node version to standardize on is `[NEEDS HUMAN INPUT]`.

### Access
- Read access to the internal npm registry (`engci-maven-master.cisco.com`) for publish/resolve of `@webex/*` artifacts; outbound egress to that host for release flows.

## Clone & Install
```bash
git clone https://github.com/webex/webex-js-sdk
cd webex-js-sdk
yarn install
```

## Build / Run / Test
| Role | Command |
|---|---|
| Install | `yarn install` |
| Build (full) | `yarn build` |
| Package (single plugin build) | `yarn workspace @webex/<plugin-name> build:src` |
| Unit test | `yarn test:unit` (all) · `yarn workspace @webex/<plugin-name> test:unit` (single) |
| Integration test | `yarn test:integration` |
| Lint / format | `yarn lint` |
| E2E / samples | `yarn test:e2e` |

All commands are read from root `package.json` `scripts`.

Run a single unit test file with `--targets`, using a path relative to the test type's spec dir
(`test:unit` → `test/unit/spec/`):
```bash
yarn workspace @webex/plugin-meetings test:unit --targets locus-info/controlsUtils.js
```

## First-Run Verification
- `yarn workspace @webex/<plugin-name> test:unit` for a small package (e.g. `@webex/common-timers`)
  completes green, confirming the workspace install and toolchain are functional.

### Artifact Registries
<!-- Include-if the build resolves from an external/authenticated registry -->
| Registry | Host | Settings file | Auth env-var names (values NOT stored) |
|---|---|---|---|
| webex-release-npm | `engci-maven-master.cisco.com` | `package.json` (`publishConfig.registry`) | `[NEEDS HUMAN INPUT]` — auth variable names not declared in committed config |

## Multi-Repo Workspace Layout
<!-- Include-if topology A -->
This product is a single git monorepo of workspace packages rather than sibling repos. The "workspace"
here is the yarn workspace tree under `packages/` (see `ARCHITECTURE.md` → Package Map). No additional
sibling-repo checkout is required for normal SDK work.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Conventions: `patterns/` + `rules/` (and `RULES.md`).
