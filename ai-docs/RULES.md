# RULES — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) · router [`SPEC_INDEX.md`](SPEC_INDEX.md).
> Enforceable do/don't beyond the AGENTS.md critical rules. Each rule names its enforcement point.

> **SDD status: assess-only draft (non-authoritative).** Rules below are drawn from the repo's real
> committed config (eslint, prettier, commitlint, husky, jest) and the routed architecture source. They
> describe what the toolchain already enforces; they add no new gate in assess-only mode.

## Coverage Map (which docs/specs to trust)
Mirrors `.sdd/manifest.json` (authoritative). Full 79-module table lives in [`SPEC_INDEX.md`](SPEC_INDEX.md).

| Module (group) | Manifest coverage state | What it means here |
|---|---|---|
| `webex-core`, `contact-center`, `internal-plugin-conversation`, `plugin-authorization(-browser/-node)`, `plugin-encryption`, `calling` | Partial | A routed source doc has been migrated by meaning into the module spec; treat the spec as a **hint** and cross-check code before relying on any detail. |
| All other packages (`plugin-*`, `internal-plugin-*`, `storage-adapter-*`, `test-helper-*`, bundles, …) | Untracked | No canonical spec yet — **code is the source of truth**; read the source, do not infer from docs. |
| Any module | Specced | None yet. When present, the spec is authoritative within its drift threshold. |

## Autonomy & Ask-First
- **May proceed:** low-risk changes that don't touch public contracts, security surfaces, or persisted
  data — e.g. a doc/comment fix, a test addition, an internal refactor behind a stable export.
- **Ask first / plan + confirm:** any change to an exported public API/event/config namespace, an OAuth or
  encryption/credential path, a storage adapter contract, the interceptor pipeline order, or a
  cross-package dependency edge.
- **Never without explicit human approval:** `git push`, publishing/release (`yarn release`), deleting
  data, or posting to trackers/PRs.

## Naming
- Packages are scoped `@webex/<name>`; public plugins are `plugin-*`, internal plugins `internal-plugin-*`,
  storage adapters `storage-adapter-*`, test helpers `test-helper-*` (from the `packages/` tree).
- Each plugin declares a `namespace` used for config and storage keying (e.g. `People`), and registers
  under a lowercase accessor (`webex.people`, `webex.internal.conversation`). Match the existing casing.
- Commit types follow Conventional Commits (see R3). Do not invent new type prefixes.

## Logging
- Use the plugin's injected `logger` (derived property on `WebexPlugin`); do not `console.log` in library
  code. Request/response logging is handled by the `RequestLogger`/`ResponseLogger` interceptors.
- Never log tokens, authorization headers, secrets, or user content. Logging interceptors must redact
  authorization material. See [`SECURITY.md`](SECURITY.md) for the never-log list.

## Error Handling
- Network/auth errors flow through the interceptor pipeline: `AuthInterceptor` handles 401 →
  re-auth/refresh/replay (bounded by `maxAuthenticationReplays`); `HttpStatusInterceptor` maps HTTP
  status to errors. Do not hand-roll 401/refresh handling outside this path.
- Surface actionable errors to callers (e.g. auth guides callers to `initiateLogin()` when refresh is
  impossible); never swallow an error silently. Basis: `webex-plugin-architecture.md` (HTTP Request
  Pipeline), routed OAuth guides.

## Imports / Dependencies
- New capability is added as a plugin extending `WebexPlugin`, registered via `webex-core`'s
  `registerPlugin(name, constructor, options)` — never bypass the core. (R8)
- Respect plugin boundaries: use the plugin's own `config`, `boundedStorage`, `unboundedStorage`; don't
  reach into another plugin's namespace. (R10)
- All authorized/service network calls go through the interceptor pipeline (auth, service resolution,
  payload transform/encryption, timing, retry). Never hand-build authorized requests. (R9)
- Workspace-internal deps are version-synced; new external deps need a lead's approval and must pass the
  committed lint/spell/license gates.

## Testing
- Each behavior change adds unit coverage (and the fitting higher tier — integration/e2e — when it crosses
  the network or browser boundary), including a negative/failure case, not only the happy path.
- Coverage bar: **85%** global lines/functions/branches/statements (`jest.config.js`
  `coverageThreshold.global`). Some `calling` sub-paths carry lower per-file overrides (documented TODO to
  raise to 85%) — confirm the effective threshold for the path you touch. (R4)
- Tests live under each package's `test/unit` and `test/integration`; e2e under `docs/samples` / WebdriverIO.

## Security
Repo-specific must-dos (full posture in [`SECURITY.md`](SECURITY.md)):
- Never log or commit tokens/secrets; `.env` is git-ignored, `.env.default` documents variable **names** only.
- Credentials are normalized and held by `webex-core`; token injection and refresh are the
  `AuthInterceptor`'s job. Encryption/decryption is the `PayloadTransformerInterceptor` + encryption plugins.
- Browser implicit-grant tokens live in the URL hash and are the less-secure option; prefer authorization
  code grant (confidential client) where a backend exists.

## Spec-Currency & Drift Thresholds
- Update the spec/docs in the SAME change as the code (spec-currency): a public-surface change updates the
  owning module spec, `CONTRACTS.md`, generated API reference, and `.sdd/manifest.json` together.
- Drift thresholds mirror the coverage-status definitions: Specced ≤5% drift, Partial 40–80% specced
  (cross-check code), Untracked <40% (code is truth). Use the repo's stricter manifest policy if one is set.

## Secrets Policy
- No hardcoded secrets/tokens/keys/connection strings — ever. Source them from environment
  (`WEBEX_CLIENT_SECRET`, `WEBEX_APPID_SECRET`, etc., names only in `.env.default`); never log or commit them.
- The npm registry (`https://registry.npmjs.org`) is public and requires no committed auth secret.
- Spelling of docs/identifiers passes `cspell.json`; secret material must never be added to the dictionary.

## Enforced by committed tooling (substrate consumed, not recreated)

| # | Rule | Enforcement point |
|---|---|---|
| R1 | Code must pass ESLint (`airbnb-base` + `prettier/recommended`). | `.eslintrc.js`; `yarn lint`; `lint-staged` on `*.{js,ts}` (`--fix`) via husky `pre-commit` |
| R2 | Formatting is Prettier-owned — do not hand-format against it. | `.prettierrc`, `plugin:prettier/recommended` |
| R3 | Commit messages follow Conventional Commits; type ∈ {build, chore, ci, docs, feat, fix, perf, refactor, revert, test}. | `commitlint.config.cjs`; husky `commit-msg` (`yarn commitlint`) |
| R4 | Unit test coverage meets the repo bar: **85%** global (lines/functions/branches/statements). | `jest.config.js` `coverageThreshold.global` |
| R5 | Spelling in docs/identifiers passes the project dictionary. | `cspell.json` |
| R6 | TypeScript typecheck must pass for typed packages. | `config/tsconfig.typecheck.json` via `yarn build:tsc` |
| R7 | Pre-commit runs `srcdist` + `lint:staged`; do not bypass hooks (except CI, which skips pre-commit by design). | `.husky/pre-commit` |
| R8 | New capability is added as a plugin extending `WebexPlugin`, registered through `registerPlugin(...)`. | `webex-plugin-architecture.md` (Plugin System) |
| R9 | All authorized/service network calls go through the interceptor pipeline. | `webex-plugin-architecture.md` (HTTP Request Pipeline) |
| R10 | Plugin config and storage are namespaced — don't reach into another plugin's namespace. | `webex-plugin-architecture.md` (Storage/Config) |
| R11 | Never invent an API/event/flag/constant — read the real file. SDD docs here are non-authoritative drafts. | AGENTS.md Critical Rule 1 |

## Maintenance
- Add a rule when a review correction recurs; remove it when a lint rule starts enforcing it.
- Cross-reference: security → [`SECURITY.md`](SECURITY.md); routed source rules → `.sdd/manifest.json`
  `spec_sources` and the owning module specs.
- Assess-only: this file surfaces existing enforcement. Rigorous mode may add repo-specific rules mined
  from real review corrections (`coding-rules-profile`).
