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

This doc also carries the developer-loop, single-package build/test, and Playwright e2e content migrated
from the repo-level dev guide and `cc_playwright/ai-docs/AGENTS.md`.

## Prerequisites

### Toolchain
| Tool | Version | Where it's pinned |
|---|---|---|
| Node | 18.x | `package.json` `engines.node` |
| yarn | 3.4.1 | `package.json` `packageManager` |

> Note: a prior dev guide referenced Node `22.14`. The committed `engines` value (18.x) is authoritative
> until the repo owner reconciles the two ([NEEDS HUMAN INPUT]).

### Access
- Read access to the internal Cisco npm registry (`webex-release-npm`, Artifactory) for publishing; outbound
  egress to that host for release builds. Public consumption is via npmjs/CDN and needs no special access.

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
| Build (single package) | `yarn workspace @webex/<plugin> build:src` |
| Unit test | `yarn test:unit` |
| Unit test (single package) | `yarn workspace @webex/<plugin> test:unit` |
| Integration test | `yarn test:integration` |
| E2E test | `yarn test:e2e` |
| Lint / format | `yarn lint` |

**Running a single test file** — pass `--targets` with a path relative to the test type's spec directory
(`test:unit` resolves from `test/unit/spec/`; `test:integration`/`test:browser` from `test/integration/spec/`):
```bash
yarn workspace @webex/plugin-meetings test:unit --targets locus-info/controlsUtils.js
```
Common mistake: passing a bare filename or full path — `--targets` must start inside the spec directory.

## First-Run Verification
- `yarn workspace @webex/plugin-<name> test:unit` for a single plugin completes green, confirming the
  workspace install and build toolchain resolve correctly.

## Configuration & Secrets
- Quick start uses `WEBEX_ACCESS_TOKEN` (via `webex/env`) to obtain an authenticated instance; obtain tokens
  from the Cisco Webex for Developers portal. Never hardcode or commit tokens (see `SECURITY.md`).

### Artifact Registries
<!-- Kept: the build resolves/publishes via an authenticated registry -->
| Registry | Host | Settings file | Auth env-var names (values NOT stored) |
|---|---|---|---|
| `webex-release-npm` | `engci-maven-master.cisco.com` | `package.json` (`publishConfig.registry`) | provided by the release pipeline (names not committed in repo config) |

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Conventions and rules: `RULES.md`.
