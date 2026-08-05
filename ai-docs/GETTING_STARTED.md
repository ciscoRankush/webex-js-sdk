<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: getting-started@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# Getting Started — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc to get a build/test loop running.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

## Prerequisites

### Toolchain
| Tool | Version | Where it's pinned |
|---|---|---|
| Node.js | 18.x | `package.json` (`engines.node`) |
| npm | >=10.5 | `package.json` (`engines.npm`) |
| Yarn | 3.4.1 | `package.json` (`packageManager`) |

### Access
- Read access to the internal Webex npm registry for publishing (`publishConfig` host
  `engci-maven-master.cisco.com`); public consumption uses public npm. Outbound egress to the registry
  host is required for install/publish.

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
| Build (full, with tests) | `yarn build` |
| Package (build without tests) | `yarn build:package` |
| Unit test | `yarn test:unit` |
| Integration test | `yarn test:integration` |
| Lint / format | `yarn lint` |

Source: root `package.json` `scripts`. There is no repo-level `run (local)` entry point (this is a
library monorepo, not a runnable service); samples are served with `yarn samples:serve`.

## First-Run Verification
- After `yarn install && yarn build`, run `yarn test:unit`; a green unit run against
  `packages/@webex/webex-core` confirms the workspace built and links resolved.

<!-- Include if: the repo needs local config / env vars / secrets to run -->
## Configuration & Secrets
- Optional network-logging env vars: `ENABLE_NETWORK_LOGGING`, `ENABLE_VERBOSE_NETWORK_LOGGING`
  (`packages/@webex/webex-core/src/webex-core.js`). Integration/sample runs need Webex credentials
  supplied via environment — never hardcode tokens (see `SECURITY.md`).

<!-- Include if: the build resolves dependencies from an external or authenticated registry -->
### Artifact Registries
| Registry | Host | Settings file | Auth env-var names (values NOT stored) |
|---|---|---|---|
| webex-release-npm | `engci-maven-master.cisco.com` | `package.json` (`publishConfig.registry`) | `[NEEDS HUMAN INPUT]` — auth variable names not evidenced in committed config |

## Dev Environment
- `[NEEDS HUMAN INPUT]` — no committed devcontainer/compose-based one-command dev environment was
  evidenced; local setup is `yarn install` + `yarn build`.

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Conventions: `patterns/` + `rules/` (and `RULES.md`).
