# AGENTS.md — webex-js-sdk

> You are the agent entry point — read first. Next: router [`SPEC_INDEX.md`](ai-docs/SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ai-docs/ARCHITECTURE.md). Load this + `SPEC_INDEX.md` first; pull module/standing docs on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; keep this file under ~200 lines.

> **SDD status: assess-only draft.** These SDD docs were generated in assess-only mode and are
> **non-authoritative**. **Code is the source of truth.** They surface what is specced vs not; they
> gate nothing. Items marked `[NEEDS HUMAN INPUT]` await confirmation before rigorous-mode generation.

## Repo Overview
**webex-js-sdk** is the public mono-repository for Cisco Webex's JavaScript SDK: ~79 workspace
packages (yarn 3.4.1) that let browsers and Node apps integrate with Webex platform services
(meetings, messaging, calling, presence, contact-center, and the supporting internal plugins).

**What it is:**
- A monorepo of publishable npm packages built on a plugin architecture over a shared `@webex/webex-core`.
- Both a unified SDK (`webex`, `webex-node`) and modular per-capability packages (`@webex/plugin-*`).

**What it is NOT:**
- ❌ A single deployable service or backend — it is a client SDK consumed by other apps.
- ❌ The owner of the Webex backend APIs — it is a client to `developer.webex.com` / internal services.
- ❌ A UI application — samples under `docs/samples/` are demos, not the product.

## Tech Stack
- JavaScript + TypeScript; Node 18.x (`.nvmrc` = `lts/jod`); Yarn 3.4.1 workspaces; npm `>=10.5`.
- State: AmpersandState. Build: bespoke `tooling/` + esbuild/tsc/babel/webpack.
- Tests: Mocha/Karma (legacy), Jest (calling & newer packages), WebdriverIO (samples e2e).

## Architecture
```
Consumer app
   │  webex.init(...) / import @webex/plugin-*
   ▼
webex / webex-node (unified bundles)  ──┐
                                        ▼
Public plugins (meetings, people, rooms, messages, authorization, encryption, presence, …)
Internal plugins (device, mercury, locus, conversation, metrics, …)
                                        ▼
@webex/webex-core  — plugin registry · HTTP interceptor pipeline · auth/credentials · storage · config · events
                                        ▼
Foundation: AmpersandState · @webex/http-core · @webex/common* · storage adapters
```
→ Full repo architecture & component responsibilities: **[ARCHITECTURE.md](./ai-docs/ARCHITECTURE.md)**

## Module / Package Structure
```
packages/@webex/webex-core/        — SDK core: plugin system, HTTP pipeline, auth, storage, config, events
packages/@webex/plugin-*/          — public plugins (meetings, people, rooms, messages, authorization*, encryption, presence, …)
packages/@webex/internal-plugin-*/ — internal plugins (device, mercury, locus, conversation, metrics, dss, …)
packages/@webex/common*, http-core, helper-*, media-helpers, webrtc — core runtime + helpers
packages/@webex/storage-adapter-*/ — bounded/unbounded storage adapters
packages/@webex/test-helper-*/, test-*, xunit-with-logs — test infrastructure
packages/webex, packages/webex-node — unified SDK entry bundles
packages/calling, packages/byods, packages/contact-center — standalone capability SDKs
packages/config/*, packages/legacy/*, packages/tools/* — build/lint/tooling workspaces
```
→ Per-module docs and the spec router: **[ai-docs/SPEC_INDEX.md](./ai-docs/SPEC_INDEX.md)**

## Critical Rules
1. **Code is the source of truth.** Never invent an API, path, event, flag, or constant — read the real file. (SDD docs here are assess-only drafts.)
2. **Ask before coding.** Present a plan / Spec Summary; wait for confirmation.
3. **Plugins extend `WebexPlugin` and register through `webex-core`.** Add capability via `registerPlugin(name, ctor, {proxies, interceptors, config, payloadTransformer})` — do not reach around the core request pipeline.
4. **All network requests go through the interceptor chain.** Auth headers, service-URL resolution, payload transform (encryption), timing, and retry are pipeline responsibilities — never hand-build authorized requests.
5. **Never log or commit tokens/secrets.** Credentials are normalized and held by `webex-core` credentials; `.env` is git-ignored.
6. **Respect semver + the changelog.** Packages are published to npm; breaking changes need a major bump and a consumer-facing changelog entry.

## Essential Commands
| Task | Command |
|---|---|
| Install | `yarn` |
| Build | `yarn build` (local dev: `yarn build:local`) |
| Test | `yarn test` (unit: `yarn test:unit`; integration: `yarn test:integration`) |
| Lint/format | `yarn lint` |

## Common Gotchas
1. **Two test stacks coexist** — legacy packages use Mocha/Karma via `tooling/index.js`; `calling` and newer packages use Jest (`jest.config.js`). Run the tier that matches the package.
2. **Coverage threshold is 85% global (`jest.config.js`)**, but several `calling` sub-paths carry lower per-file overrides; a global 85 does not mean every path is at 85.
3. **`distsrc` rewrites `main` from `dist/` to `src/`** for local dev — don't commit that flip.
4. **Build order matters** — `yarn build` bootstraps `media-helpers`, `calling`, and tools first; running a package build in isolation can fail on unbuilt workspace deps.
5. **Integration tests need real credentials/services** (`.env`, test users) — they are not hermetic.

## Pre-Commit Checklist
- [ ] Tests pass; coverage meets the repo bar (85% global)
- [ ] Spec/docs updated in the same change (spec-currency) — including affected module spec
- [ ] No hardcoded secrets; inputs validated; nothing logs tokens
- [ ] `yarn lint` clean; commit message passes commitlint

---
**SDD coverage:** this repo's per-module coverage state lives in `.sdd/manifest.json` (mirror in
`ai-docs/SPEC_INDEX.md`). Manifest is **non-authoritative (assess-only)**: treat code as source of
truth and cross-check before relying on any spec.
