<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
  source_material: eslintrules/ (10-15 real files sampled), commitlint.config.cjs, docs/dependency/audit-process.md, jest.config.js
-->

# RULES.md — webex-js-sdk

> Coding conventions extracted from real source files in this repo. Not invented best practices.
> Enforce in PR review and CI.

## coverage map (which docs/specs to trust)

| Document | Trust level | When to load |
|---|---|---|
| `ai-docs/AGENTS.md` | **Authoritative** — load first for all tasks | Orientation; routing decisions |
| `ai-docs/SPEC_INDEX.md` | **Authoritative** — load second | Module routing; coverage status |
| `ai-docs/ARCHITECTURE.md` | **Authoritative** | Cross-module dependency context; component responsibilities |
| `ai-docs/RULES.md` (this file) | **Authoritative** | Coding conventions; PR review checklist |
| `ai-docs/SECURITY.md` | **Authoritative** | Security constraints; secret handling |
| `ai-docs/CONTRACTS.md` | **Authoritative** | Published API surfaces; external service dependencies |
| `ai-docs/GLOSSARY.md` | **Authoritative** | Domain term definitions |
| Per-module `*-spec.md` (Partial coverage) | **Authoritative for covered modules** | Module-level design; public API surface |
| `packages/@webex/<module>/src/` | **Ground truth** — always verify here | Any API, event, flag, or constant claimed to exist |
| `internal-README.md` files | **Not canonical** — developer notes only | Do not treat as design specifications |
| `UPGRADING.md`, `BUILD-ISSUES.md` | **Context only** | Migration notes; known build issues |

**When spec and code conflict, code wins.** Read the source file before proposing a change.

## autonomy & ask-first

**Operate autonomously (no confirmation needed):**
- Reading source files, running lint, running unit tests
- Making changes within a single package that do not affect public API surface
- Fixing lint errors, typos, or test failures introduced in the current branch
- Adding or updating JSDoc/TSDoc for existing functions

**Ask first before proceeding:**
- Adding a new `peerDependency` or cross-workspace dependency edge
- Changing `registerPlugin` / `registerInternalPlugin` registration for any module
- Lowering a Jest coverage threshold
- Modifying the `browser` shim map in any `package.json`
- Changing build toolchain configuration (`webex-legacy-tools`, `tsc`, webpack, rollup)
- Adding a dependency flagged as unmaintained or deprecated
- Any change to `.github/` workflows or CI/CD configuration

**Never do without explicit instruction:**
- Commit directly to `main`
- Skip `commitlint` validation
- Publish a package manually without `build:publish`
- Add a `devDependency` already declared in the root `package.json` to an individual package
- Lower a security-related ESLint rule severity

## naming

*Evidence: `eslintrules/style.js`, `eslintrules/best-practices.js`*

- **camelCase for all identifiers.** `camelcase: error` is enforced. No snake_case in internal SDK code.
- **Remap API response keys at the boundary.** External API responses may use snake_case — convert in an interceptor or model constructor before passing values into plugin internals.
- **Plugin names are PascalCase classes**, registered under a camelCase name string (e.g. `registerPlugin('meetings', Meetings)`).
- **Event names** follow the existing AmpersandJS convention: `change:<property>` for state changes, domain-specific strings for business events (e.g. `mercury:event`, `task:incoming`).
- **File names** for legacy JS packages use camelCase (`authInterceptor.js`). TypeScript packages use PascalCase for class files (`CallingClient.ts`) and camelCase for utility files.
- **Test files** mirror the source file path under `test/unit/` or `test/integration/` with a `.spec.js` / `.spec.ts` suffix.

## logging

*Evidence: `packages/@webex/plugin-logger/`, `packages/calling/src/Logger/`*

- **Use the SDK logger, not `console.log`.** Legacy packages use `this.webex.logger`; `@webex/calling` uses its internal `Logger` class.
- **Never log access tokens or credentials.** `plugin-logger` must redact sensitive fields. [NEEDS HUMAN INPUT — confirm redaction configuration]
- **Log levels:** `error` for unrecoverable failures, `warn` for recoverable unexpected states, `info` for lifecycle events (connect, ready, logout), `debug` for detailed tracing (off in production).
- **Structured logging preferred** — include context object with relevant IDs (e.g. `{meetingId, deviceId}`) rather than embedding them in the message string.

## error handling

*Evidence: `eslintrules/best-practices.js`, `packages/@webex/http-core/src/`*

- **Promises must have `.catch()` or be returned.** Unhandled promise rejections are a runtime error and a lint violation.
- **`consistent-return` rule:** functions either always return a value or always use bare `return`. Do not mix.
- **HTTP errors:** `@webex/http-core` wraps responses in typed error objects. Catch by error type, not by HTTP status string.
- **Plugin initialization errors** must reject the plugin's `initialize()` promise so `WebexCore` can surface them via the `ready` event failure path.
- **Do not swallow errors silently.** If an error is caught and not re-thrown, it must be logged at `warn` or `error` level.

## imports / dependencies

*Evidence: `eslintrules/imports.js`, `package.json` workspaces, `.yarnrc.yml`*

- **Workspace cross-references use `workspace:*`.** Never pin a local package to a version number or use a relative path `../../` import.
- **No circular workspace dependencies.** Topological build order will break silently at runtime if cycles exist.
- **Legacy packages use CJS-compatible imports.** Do not use native `import`/`export` at runtime in Babel-compiled packages.
- **TypeScript packages** (`@webex/calling`, `@webex/byods`, `@webex/contact-center`) use native ESM `import`/`export`.
- **Do not add a `devDependency` to a package if it already exists in the root `package.json`.** This causes version drift.
- **Every new direct dependency** must be evaluated against the unmaintained/deprecated audit process before merging.

## testing

*Evidence: `jest.config.js`, `eslintrules/mocha.js`, root `package.json`*

- **Global coverage floor: 85%** (lines, functions, branches, statements). Per-package exceptions are in `jest.config.js` and require explicit approval to lower.
- **Unit tests:** `<package>/test/unit/` directory, Jest framework, `*.spec.js` / `*.spec.ts` suffix.
- **Integration tests:** `<package>/test/integration/` directory, Mocha framework.
- **Browser tests:** Karma runner (configured separately).
- **Do not mix unit and integration tests** in the same directory.
- **Use `@webex/test-helper-*` packages** for shared test utilities — do not vendor helpers into individual packages.
- **Mocha rules apply to integration test files** — use `describe`/`it`/`before`/`after` only; `eslintrules/mocha.js` is loaded for those files.
- **Test changes must accompany production code changes** (and vice versa for bug fixes).

## security

*Evidence: `SECURITY.md`, `.coderabbit.yaml`, `eslintrules/`*

- **No secrets in source.** CodeRabbit `gitleaks` runs on every PR. `WEBEX_ACCESS_TOKEN`, `client_secret`, and similar must never be committed.
- **`client_secret` is server-side only.** Never pass it to browser-side code or include it in a bundle.
- **Access tokens must not be logged.** Configure `plugin-logger` to redact credentials.
- **CSRF state parameter** must not be removed from the browser OAuth flow.
- **Input from external API responses must be validated** before use in security-sensitive code paths (e.g., before passing to `eval`, `innerHTML`, or URL construction).
- **KMS operations require the `spark:kms` OAuth scope** — never silently drop the scope or the encryption pipeline will fail.
- See `ai-docs/SECURITY.md` for the full security posture.

## spec-currency & drift thresholds

- **Spec → Code drift threshold: 1 major version.** If a spec describes an API that no longer exists in `src/`, the spec is stale and must be updated before the spec is used to make changes.
- **Untracked modules** have no spec — read `src/index.js` directly and do not invent behavior.
- **Partial-coverage modules** have specs for the sections listed; for uncovered sections, read the source.
- **When in doubt, the source file is authoritative.** A spec claim that contradicts the source file is a spec error, not a code error.
- **Spec updates** should be proposed alongside the code change that makes the spec stale (same PR).

## secrets policy

- **Never commit** secrets, tokens, or credentials. This applies to: `WEBEX_ACCESS_TOKEN`, `WEBEX_CLIENT_SECRET`, JWT guest issuer credentials, private keys, API keys, or internal endpoint credentials.
- **Development secrets** go in a `.env` file (gitignored); CI secrets go in GitHub Actions secrets; production secrets go in a secrets manager.
- **CodeRabbit `gitleaks`** is enabled on all PRs — a secret detected in a PR will block merge.
- **If a secret is accidentally committed:** immediately rotate the secret in the issuing system, then remove it from git history using `git filter-repo` (not `git rm`). Notify the security team.
- **Environment variable naming convention:** `WEBEX_*` prefix for all SDK-related secrets (consistent with `ENV.md`).

## maintenance

- **This file should be updated** when ESLint rules change (`eslintrules/`), when the test framework changes, or when a new cross-cutting convention is established.
- **Do not add rules that are already enforced by ESLint** without noting the ESLint rule name — duplication with no ESLint backing tends to drift.
- **Owner:** [NEEDS HUMAN INPUT] — Assign a DRI for keeping these rules current.

## Language and Style

*Evidence: `eslintrules/best-practices.js`, `eslintrules/style.js`, `eslintrules/es6.js`*

- **camelCase is mandatory.** `camelcase: error` enforced by ESLint. Never use snake_case identifiers in SDK code. Remap API response keys at the boundary (e.g. in an interceptor or model constructor), not throughout internal code.
- **Strict equality always.** `eqeqeq: [error, always]` — use `===` and `!==` everywhere. No `==` or `!=`.
- **No implicit returns through inconsistency.** `consistent-return: error` — a function either always returns a value or never does. Mixed `return;` and `return value;` in the same function is a lint error.
- **Array callbacks must return.** `array-callback-return: error` — `.map()`, `.filter()`, `.reduce()` callbacks must have explicit `return` statements.
- **Block-scoped variables.** `block-scoped-var: error` — `var` declarations are treated as block-scoped; use `let`/`const` for new code.
- **Guard for-in loops.** `guard-for-in: error` — `for (key in obj)` must be wrapped in `if (obj.hasOwnProperty(key))` or use `Object.keys()` instead.
- **Dot notation preferred.** `dot-notation: error` — use `obj.prop` not `obj['prop']` unless the key is dynamic.
- **Curly braces on all control structures.** `curly: error` — no single-line `if`/`else`/`for`/`while` without braces.
- **Switch default case required.** All `switch` statements must have a `default` branch.
- **Stroustrup brace style.** `brace-style: stroustrup` — `else`/`catch`/`finally` on a new line after the closing `}`.
- **No trailing commas.** `comma-dangle: never` — no trailing commas in arrays, objects, or parameter lists.
- **Array brackets: no spaces.** `array-bracket-spacing: never`.

## Module and Import Conventions

*Evidence: `eslintrules/imports.js`, `eslintrules/es6.js`, `package.json` workspaces*

- **No circular dependencies.** The build pipeline enforces topological workspace order; circular workspace dependencies will cause silent runtime failures.
- **Workspace imports use `workspace:*`.** Internal cross-package dependencies must use the `workspace:*` protocol in `package.json`, never a version pin or relative path.
- **Legacy packages use CJS-compatible Babel output.** Do not use native ESM `import`/`export` at runtime in legacy `@webex/*` packages — Babel compiles them; follow the existing pattern in the package.
- **TypeScript-only packages** (`@webex/calling`, `@webex/byods`) use native `import`/`export` compiled by `tsc`.

## Plugin Registration Pattern

*Evidence: `packages/@webex/internal-plugin-mercury/src/index.js`, `packages/@webex/webex-core/src/index.js`*

- **All public plugins:** `registerPlugin(name, Plugin, opts)` from `@webex/webex-core`.
- **All internal plugins:** `registerInternalPlugin(name, Plugin, opts)` from `@webex/webex-core`. Internal plugins are not exposed on the public `webex` surface.
- **`onBeforeLogout` hook:** plugins that hold stateful connections (e.g., Mercury WebSocket) must implement `onBeforeLogout()` returning a Promise.
- **Plugin classes extend AmpersandState** — do not use plain ES classes or EventEmitter directly as a plugin base.

## Testing Rules

*Evidence: `jest.config.js`, root `package.json` test scripts, `eslintrules/mocha.js`*

- **Global coverage floor: 85%** on lines, functions, branches, and statements. Per-module exceptions exist (see `jest.config.js`) but require explicit approval to lower.
- **Test directories:** unit tests live under `<package>/test/unit/`; integration tests under `<package>/test/integration/`. Never mix unit and integration in the same directory.
- **Test helpers are internal packages.** Use `@webex/test-helper-*` workspace packages — do not vendor test utilities into individual packages.
- **Mocha rules apply to integration tests.** `eslintrules/mocha.js` is loaded for test files; use `describe`/`it`/`before`/`after` patterns only.

## Commit and PR Discipline

*Evidence: `commitlint.config.cjs`, `.github/PULL_REQUEST_TEMPLATE.md`*

- **Conventional commit format is enforced.** `<type>(<scope>): <subject>`. Allowed types: `build | chore | ci | docs | feat | fix | perf | refactor | revert | test`.
- **Scope is optional but recommended** for targeted changes (e.g., `feat(calling): add hold/resume`).
- **No force-push to `main`.** All changes go through a PR; `main` is protected.
- **PR title must pass `pr-title-checker.yml`** — the PR title itself must follow the conventional commit format (checked separately from commit messages).

## Dependency Governance

*Evidence: `docs/dependency/audit-process.md`, `docs/dependency/unmaintained-catalog.md`*

- **Monthly audit cadence** for unmaintained/suspect dependencies. A designated developer runs the audit script and performs manual triage.
- **Quarterly review** of the full unmaintained catalog to validate past decisions.
- **Every suspect package must have a recorded decision:** `REPLACE`, `FORK_MAINTAIN`, `ACCEPT`, or `False Positive`, with justification in `docs/dependency/unmaintained-catalog.md`.
- **`ACCEPT` decisions must include explicit justification.** "It works" is not a justification. Evidence of active maintenance, security review, or no viable alternative is required.
- **Critical = 0 target.** Deprecated + vulnerable packages must be eliminated. High-severity items should be reduced quarter over quarter.
- **Never silently add a deprecated package** as a direct dependency. Run the audit script, get a triage decision recorded, and only then accept it.

## Build and Platform Rules

*Evidence: `package.json`, `tsconfig.json`, `.yarnrc.yml`, `amplify.yml`*

- **`emitDeclarationOnly: true` in root tsconfig.** Babel handles transpilation for legacy packages. Never set `emit: true` for legacy packages; it will produce duplicate output.
- **Browser shim maps are required** for any Node-only API used in packages with a `browser` field in `package.json`. Add a `.shim.js` file and map it in the package's `browser` field.
- **`yarn install --immutable`** is the CI install command (per `amplify.yml`). Never use `npm install` in this repo.
- **Build order is topological.** Tools workspace (`@webex/*-tools`) must build before core packages, which must build before plugin packages. Violating this order causes downstream import errors.
- **`yarn workspaces foreach --parallel --topological`** is the standard invocation for workspace-wide commands. Using `--parallel` alone (without `--topological`) on build tasks is an error.

## Code Review Checklist (extract)

*Evidence: `.coderabbit.yaml` (Chill profile, 18+ linters including semgrep and gitleaks)*

- All `camelCase` renaming of API responses at boundaries — not threaded through internal code
- All new exports have JSDoc/TSDoc coverage (eslint-plugin-jsdoc/eslint-plugin-tsdoc enabled)
- No secrets, tokens, or credentials in source (gitleaks enabled in CodeRabbit)
- No unused variables (`no-unused-vars` in ESLint rules)
- Test file changes come with corresponding production code changes (and vice versa for bug fixes)
