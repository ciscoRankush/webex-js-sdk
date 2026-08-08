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

## Consuming the SDK (Install & Usage)

> The Cisco Webex JS SDK

### Install the published package

```bash
npm install --save webex
```

### Authenticated usage

> All of the examples in these API docs assume you've gotten an authenticated Webex instance (unless otherwise specified) using one of the methods below.

`webex` has three basic modes of operation:

#### Shell Script (Quick Start)

This is the quickest way to get up and running with our JavaScript SDK. Simply set the environment variable `WEBEX_ACCESS_TOKEN` to your access token and add the following line at the top of your JavaScript file to get a ready-to-use instance.

> You can get your `WEBEX_ACCESS_TOKEN` from the [Cisco Webex for Developers portal](https://developer.webex.com).

```js
const webex = require(`webex/env`);
```

> `webex/env` is also a great way to get started with [bots](https://developer.webex.com/docs/bots).

#### Browser

Our JavaScript SDK provides out-of-the-box support for the [OAuth 2.0 Implicit Grant Flow](https://tools.ietf.org/html/rfc6749#section-4.2).

> You'll need to [register an OAuth Client](https://developer.webex.com/my-apps/new/integration) to get your "authorization string"

Use the steps under [Bundling](#bundling) (or something similar) to get the SDK into your browser, then use the following JavaScript to get started:

```js
const webex = Webex.init({
  config: {
    authorizationString: <your auth URL>,
  }
});

webex.once(`ready`, () => {
  if (webex.canAuthorize) {
    /* Your application code goes here */
  }
  else {
    /* Your login code goes here */

    /*
      The following is a naive example of how to log in a user. Note that login should probably require a user action, otherwise errors can lead you into an infinite redirect loop.

      This will direct the user agent to the Cisco login page. Once the user logs in, they'll be redirected back to your app and the SDK will handle parsing the URL.
    */
    webex.authorization.initiateLogin();
  }
});
```

#### Bundling

You'll need to bundle the SDK to use it in a web browser. Right now, we do all our SDK testing with [Browserify](http://browserify.org/), but our [Cisco Webex Widgets](https://github.com/webex/react-ciscospark) use [webpack](https://webpack.github.io/).

The following snippet is the bare minimum to get our code into a form suitable for a web browser. You'll probably want to additionally pipe it through a minifier like [UglifyJS](https://github.com/mishoo/UglifyJS2) before going to production.

```bash
npm install webex
npm install -g browserify
echo "window.webex = require('webex')" > ./index.js
browserify index.js > bundle.js
```

Then, just load your bundle using:

```html
<script src="/webex.min.js"></script>
```

#### NodeJS

Though the implicit flow is great for single page apps, it's not ideal for integrations that might need to do things on your users' behalf months in the future. We additionally support the [OAuth 2.0 Authorization Code Grant](https://tools.ietf.org/html/rfc6749#section-4.1) flow, but due to its complexity, there's a bit you'll need to wire up in your app to take advantage of it. The following is an example of how an Express app might do authentication.

```js
var Webex = require('webex');
const assert = require(`assert`);

app.use(function(req, res, next) {
  req.webex = Webex.init({
    config: {
      credentials: {
        authorizationString: <your auth URL>,
        client_secret: <your client secret>
      },
    }
  });

  req.webex.once(`ready`, next);
});

app.get(`/login`, (req, res) => {
  // buildLoginUrl() defaults to the implicit grant flow so explicitly pass
  // `confidential` to generate a URL suitable to the Authorization Code grant
  // flow.
  res
    .redirect(req.webex.credentials.buildLoginUrl({clientType: 'confidential'}))
    .end();
});

app.get(`/oauth/redirect`, (req, res, next) => {
  assert(req.params.code);
  req.webex.authorization.requestAuthorizationCodeGrant(req.params)
    .then(() => {
      res.redirect(`/`).end();
    })
    .catch(next);
});
```

## Where to Go Next
- Agent entry: `../AGENTS.md` · System shape: `ARCHITECTURE.md` · Routing: `SPEC_INDEX.md`
- Conventions: `patterns/` + `rules/` (and `RULES.md`).
