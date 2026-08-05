<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: getting-started@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->

# GETTING STARTED — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md).
> This is the developer environment, command surface, and workflow reference. Migrated by meaning from the repository's original root `AGENTS.md` developer guide.

## Environment

The code in this repository requires Node.js. The workspace `package.json` `engines` field declares `node: 18.x` and `npm: >=10.5`; some packages (for example `@webex/calling` and `@webex/contact-center`) document Node **22.14** for local development. Use the Node version the target package documents. With `nvm`:

```
nvm install 22.14
nvm use 22.14
```

The package manager is Yarn `3.4.1` (`packageManager` in `package.json`).

## Repository Structure

This repository is a Yarn-workspaces monorepo containing the JS-SDK library, split into multiple plugins. Each plugin has its own folder inside `packages/@webex/` (with additional workspaces under `packages/webex`, `packages/webex-node`, `packages/calling`, `packages/byods`, `packages/config/*`, `packages/legacy/*`, and `packages/tools/*`).

Each plugin has its own `src` folder with source code and `test` folder with unit tests; their subfolder structures mirror each other. For example, `packages/@webex/plugin-meetings/src/meeting/index.ts` holds the `Meeting` class source, while `packages/@webex/plugin-meetings/test/unit/spec/meeting/index.js` holds its unit tests.

Usually, when working on new features or fixing bugs, you work on a **single plugin** — so when analyzing code or running tests, focus only on that plugin.

## Command Surface

The everyday loop is mirrored in [`AGENTS.md`](../AGENTS.md); the full surface follows.

| Role | Command | Source |
|---|---|---|
| Install | `yarn install` | `package.json` |
| Build (workspace) | `yarn build` | `package.json` scripts |
| Build one package | `yarn workspace @webex/<plugin-name> build:src` | root `AGENTS.md` |
| Unit tests (one package) | `yarn workspace @webex/<plugin-name> test:unit` | root `AGENTS.md` |
| Integration tests | `yarn test:integration` | `package.json` scripts |
| Lint | `yarn lint` | `package.json` scripts |
| E2E | `yarn test:e2e` | `package.json` scripts |
| API docs | `yarn build:docs` | `package.json` scripts |

### Building a single plugin

```
yarn workspace @webex/<plugin-name> build:src
```

### Unit tests for a single plugin

```
yarn workspace @webex/<plugin-name> test:unit
```

For example, for plugin-meetings:

```
yarn workspace @webex/plugin-meetings test:unit
```

### Running a specific test file

Use `--targets` with a path relative to the test type's spec directory:
- `test:unit` resolves from `test/unit/spec/`
- `test:integration` and `test:browser` resolve from `test/integration/spec/`

```
yarn workspace @webex/<plugin-name> test:unit --targets <path-relative-to-spec>
```

For example, to run `packages/@webex/plugin-meetings/test/unit/spec/locus-info/controlsUtils.js`:

```
yarn workspace @webex/plugin-meetings test:unit --targets locus-info/controlsUtils.js
```

**Common mistake:** passing just the filename (`--targets controlsUtils.js`) or a full path. The `--targets` value must be the path starting **inside** the spec directory for that test type.

## Toolchain & Registries

- **Node:** `18.x` (engines); `22.14` documented for some packages' local dev.
- **npm:** `>=10.5`; **Yarn:** `3.4.1`.
- **Build:** Babel + `tsc` (`config/tsconfig.typecheck.json`) orchestrated through `tooling/index.js`.
- **Publish registry:** `http://engci-maven-master.cisco.com/artifactory/api/npm/webex-release-npm` (`publishConfig` in `package.json`).

## Test Writing Guidelines

When adding tests to existing test files:
- Use the same coding style as the existing tests.
- Use `sinon` for mocks and stubs.
- Use `assert` from `@webex/test-helper-chai` for assertions and checks.
- Prefer `assert.calledOnceWithExactly` over multiple calls like `assert.calledOnce()` followed by `assert.calledWith()`.
- Use `sinon.useFakeTimers()` to control time progression in unit tests.
- When there are more than 3 similar test cases, use parametrized tests.
- Avoid test-code duplication; reuse existing helper test methods or write new ones.

> `plugin-meetings` unit tests are slow — always run only the tests you care about by temporarily adding `.only` (e.g. `it.only(...)`) and removing it once finished.

## Next Steps

- Repo-wide conventions and constraints: [`RULES.md`](RULES.md)
- System architecture: [`ARCHITECTURE.md`](ARCHITECTURE.md)
- Public surface index: [`CONTRACTS.md`](CONTRACTS.md)
- Module/package specs: [`SPEC_INDEX.md`](SPEC_INDEX.md)
