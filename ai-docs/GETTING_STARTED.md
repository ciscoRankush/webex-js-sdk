<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: getting-started@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# Getting Started — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc to get a build/test loop running.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

## Prerequisites

### Toolchain
| Tool | Version | Where it's pinned |
|---|---|---|
| Node.js | 18.x | `package.json` `engines.node` |
| npm | >=10.5 | `package.json` `engines.npm` |
| Yarn | 3.4.1 | `package.json` `packageManager` |

### Access
- Read access to the internal npm registry (`engci-maven-master.cisco.com`) for publishing; general
  development installs from the default public registry. Outbound egress to package registries is
  required for `yarn install`.

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
| Package (build sources) | `yarn build:package` |
| Unit test | `yarn test:unit` |
| Integration test | `yarn test:integration` |
| Lint / format | `yarn lint` |

Per-package loops exist too (e.g. `yarn workspace @webex/webex-core run test:unit`); each package
defines its own `build:src` / `test:*` scripts.

## First-Run Verification
- After `yarn install` and `yarn build`, run `yarn workspace @webex/webex-core run test:unit`; a green
  unit run confirms the toolchain and workspace wiring are working.

<!-- Include if: the build resolves dependencies from an external or authenticated registry -->
### Artifact Registries
| Registry | Host | Settings file | Auth env-var names (values NOT stored) |
|---|---|---|---|
| `webex-release-npm` | `engci-maven-master.cisco.com` | `package.json` `publishConfig` | `[NEEDS HUMAN INPUT]` (publish credentials provided by CI; variable names not confirmed) |

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Conventions: `RULES.md`.
