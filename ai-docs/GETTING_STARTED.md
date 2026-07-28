<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
  source_material: README.md (migrate-existing), documentation/webex.md (migrate-existing), ENV.md (context)
-->

# GETTING_STARTED.md — webex-js-sdk

> Developer getting-started guide for the Cisco Webex JavaScript SDK.
> Source material: `README.md`, `documentation/webex.md`, `ENV.md`

## prerequisites

- **Node.js** 18.x (minimum; check with `node --version`)
- **npm** >= 10.5 (for corepack; check with `npm --version`)
- **Corepack** enabled — Yarn 3.4.1 is managed via corepack: `corepack enable`
- **Git** 2.x+
- A Webex developer account and OAuth client registration at [developer.webex.com](https://developer.webex.com) (for auth flows in samples)
- `WEBEX_ACCESS_TOKEN` environment variable or OAuth credentials available for integration tests

## clone & install

```bash
git clone https://github.com/webex/webex-js-sdk.git
cd webex-js-sdk

# Enable Yarn via corepack (run once per machine)
corepack enable

# Install all workspace dependencies
yarn install
```

The monorepo uses Yarn 3 workspaces. All ~74 packages are linked locally after `yarn install`. Do not use `npm install` — the repo is not compatible with npm workspaces.

## build / run / test

```bash
# Build all packages for local development (topological order)
yarn build:local

# Run all unit tests (Node, all packages)
yarn test:unit

# Run integration tests
yarn test:integration

# Lint all packages
yarn lint

# Build API documentation
yarn build:docs
# Output: docs/api/

# Serve sample applications
yarn samples:build
yarn samples:serve
# Available at https://localhost:8000/samples/
```

## first-run verification

After `yarn install` and `yarn build:local`, verify the setup is working:

```bash
# 1. Confirm build succeeded — check for dist/ directories in key packages
ls packages/webex/dist/index.js
ls packages/@webex/webex-core/dist/

# 2. Run unit tests — should pass with ≥85% coverage
yarn test:unit

# 3. Quick smoke test with a PAT (get one from developer.webex.com)
WEBEX_ACCESS_TOKEN=<your-token> node -e "
  const webex = require('./packages/webex/src/index.js');
  // or: const webex = require('webex/env');  after full build
  console.log('SDK loaded:', typeof webex);
"
```

If `yarn test:unit` fails on a fresh clone, check:
- Node version (`node --version` must be 18+)
- Yarn version (`yarn --version` must be 3.4.x)
- Run `yarn install` again to ensure all workspace links are up-to-date

## where to go next

- **Architecture overview** → [`ai-docs/ARCHITECTURE.md`](ARCHITECTURE.md)
- **Coding rules and conventions** → [`ai-docs/RULES.md`](RULES.md)
- **All modules and their specs** → [`ai-docs/SPEC_INDEX.md`](SPEC_INDEX.md)
- **OAuth browser flow** → `packages/@webex/plugin-authorization-browser/BROWSER-OAUTH-FLOW-GUIDE.md`
- **OAuth Node.js flow** → `packages/@webex/plugin-authorization-node/NODE-OAUTH-FLOW-GUIDE.md`
- **Contact Center SDK lab** → `docs/labs/contact-center/README.md`
- **Encryption plugin quickstart** → `packages/@webex/plugin-encryption/developer-quickstart.md`
- **Creating a new package** → See "Creating a New Package" section below

## Install

```bash
npm install --save webex
# or
yarn add webex
```

## Usage Modes

The SDK has three basic modes of operation:

### 1. Shell Script / Quick Start (Node.js env variable)

```js
// Set WEBEX_ACCESS_TOKEN in your environment, then:
const webex = require('webex/env');
// webex is immediately authenticated and ready
```

Get your access token from [developer.webex.com](https://developer.webex.com).

This is also the recommended pattern for **bots**.

### 2. Browser (OAuth 2.0 Implicit Grant)

```html
<!-- Bundle webex into your app first (see Bundling below) -->
<script>
const webex = Webex.init({
  config: {
    authorizationString: '<your auth URL>',
  }
});
webex.once('ready', function() {
  // SDK is authenticated and ready
});
</script>
```

Register an OAuth Client at [developer.webex.com/my-apps/new/integration](https://developer.webex.com/my-apps/new/integration) to get your authorization string.

#### Bundling for Browser

```js
// webpack.config.js
module.exports = {
  entry: './src/index.js',
  // webex ships with pre-built UMD bundles in packages/webex/umd/
};
```

Pre-built UMD bundles are available in `packages/webex/umd/`.

### 3. Node.js (Server-side)

```js
const Webex = require('webex');
const webex = Webex.init({
  credentials: {
    access_token: process.env.WEBEX_ACCESS_TOKEN,
  },
});
```

## Environment Variables

*Source material: `ENV.md`*

| Variable | Description |
|---|---|
| `WEBEX_ACCESS_TOKEN` | Personal access token for quick-start and bot authentication |
| `WEBEX_CLIENT_ID` | OAuth client ID for authorization flows |
| `WEBEX_CLIENT_SECRET` | OAuth client secret (server-side only) |
| `WEBEX_REDIRECT_URI` | OAuth redirect URI |
| `WEBEX_SCOPE` | OAuth scopes (e.g., `spark:all spark:kms`) |
| [NEEDS HUMAN INPUT] | Additional environment variables — see `ENV.md` |

## Development Setup

*Source material: `CONTRIBUTING.md`*

### Requirements

- Node.js 18.x
- npm >= 10.5
- Yarn 3.4.1 (installed automatically via `corepack`)

### Clone and Install

```bash
git clone https://github.com/webex/webex-js-sdk.git
cd webex-js-sdk
yarn install
```

### Build (local development)

```bash
# Build all packages for local development
yarn build:local
```

### Run Tests

```bash
# Unit tests (Node, all packages)
yarn test:unit

# Integration tests (browser)
yarn test:integration

# Lint
yarn lint
```

### Build Documentation

```bash
yarn build:docs
# Outputs to docs/api/
```

## Sample Applications

Sample apps live in the `packages/webex/` package and are built with:

```bash
yarn samples:build
yarn samples:serve
# Serves at https://localhost:8000/samples/
```

## Creating a New Package (Contributing)

1. Create `packages/@webex/<your-package>/` following the structure of an existing package
2. Add a `package.json` with `"name": "@webex/<your-package>"` and `"devMain"` pointing to `src/index.js`
3. Add the workspace to the root `package.json` `workspaces` array
4. Use `yarn install` to link the workspace
5. Follow the plugin registration pattern if your package registers a Webex plugin (see `AGENTS.md`)
6. Add an `ai-docs/<your-package>-spec.md` spec (see `ai-docs/SPEC_INDEX.md` for the template)

## Further Reading

- [Architecture overview](ARCHITECTURE.md)
- [Coding rules and conventions](RULES.md)
- [All modules and their specs](SPEC_INDEX.md)
- [OAuth browser flow](../packages/@webex/plugin-authorization-browser/BROWSER-OAUTH-FLOW-GUIDE.md)
- [OAuth Node.js flow](../packages/@webex/plugin-authorization-node/NODE-OAUTH-FLOW-GUIDE.md)
- [Contact Center SDK lab](../docs/labs/contact-center/README.md)
- [Encryption plugin quickstart](../packages/@webex/plugin-encryption/developer-quickstart.md)
