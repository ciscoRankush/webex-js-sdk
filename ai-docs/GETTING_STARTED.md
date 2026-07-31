<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: getting-started@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Getting Started — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md). Then this doc to get a build/test loop running.
> Context-efficiency: link to canonical docs — don't duplicate them; load on demand, not upfront.

## Prerequisites

- Node 18.x, Yarn 3.4.1 (Yarn is the package manager; `packageManager: yarn@3.4.1` in `package.json`).
- The full `webex-js-sdk` monorepo checkout (these packages depend on many `workspace:*` siblings such as `@webex/webex-core`).

## Clone & Install

```bash
git clone https://github.com/webex/webex-js-sdk
cd webex-js-sdk
yarn
```

## Build / Run / Test

| Task | Command |
|---|---|
| Build | `yarn build` (or `yarn workspace @webex/plugin-authorization-browser run build:src` for one package) |
| Run (local) | n/a — libraries, consumed via `Webex.init(...)`; use `yarn samples:serve` for the SDK samples |
| Test | `yarn test:unit` (or per package: `yarn workspace @webex/plugin-authorization-browser run test:unit`) |
| Lint / format | `yarn lint` |

## First-Run Verification

- Run `yarn workspace @webex/plugin-authorization-browser run test:unit` and confirm the authorization spec suite passes (`packages/@webex/plugin-authorization-browser/test/unit/spec/authorization.js`).

## Configuration & Secrets

- Required config is passed to `Webex.init({credentials: {...}})`: `client_id`, `redirect_uri`, `scope`, `clientType` (browser); plus `client_secret` for Node/confidential clients. Obtain the client secret from your IdBroker app registration — never hardcode (see `SECURITY.md`).

## Where to Go Next

- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Conventions: `RULES.md`.
