<!-- sdd-generated-metadata
doc_kind: agent-entry
generated_from: agents@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-04T00:00:00Z
validation_status: not-run
-->
# AGENTS.md — webex-js-sdk

> You are the agent entry point — read first. Next: router [`SPEC_INDEX.md`](ai-docs/SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ai-docs/ARCHITECTURE.md). Load this + `SPEC_INDEX.md` first; pull module/standing docs on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; keep this file under ~200 lines.

> Cross-tool context file. Auto-loaded by AI coding agents. If a module ships its own `AGENTS.md`
> agent-entry file, that file layers on top of this root file. (A module's high-level design lives in
> the manifest-routed module spec, source-local as `<module-path>/ai-docs/<module-name>-spec.md`.)

> **Onboarding status:** assess-only draft. Only `@webex/webex-core` is currently tracked (migrated
> from `webex-plugin-architecture.md`); every other `packages/**` workspace is `Untracked` and code is
> the source of truth for it. See `.sdd/manifest.json` for authoritative coverage state.

## Repo Overview
**webex-js-sdk** is a Yarn-workspaces mono-repository of Cisco's public Webex JavaScript SDK packages —
the unified `webex` module plus scoped `@webex/*` plugins, internal plugins, storage adapters, and build
tooling — that let applications integrate with Webex platform services.

**What it is:**
- A monorepo of publishable npm packages (`packages/webex`, `packages/@webex/*`, `packages/calling`, `packages/byods`).
- A plugin framework: `@webex/webex-core` provides the foundation all other plugins build on.
- Multi-target: consumable in Node.js and in the browser (UMD/CDN bundle).

**What it is NOT:**
- ❌ A deployed backend service (packages are published to npm, not deployed to infra).
- ❌ The Webex platform/backend itself — it is a client SDK that calls Webex services.
- ❌ A single-package repo — do not assume one build; each workspace has its own scripts.

## Tech Stack
- JavaScript + TypeScript, Node 18.x, Yarn 3.4.1 workspaces (`package.json`).
- Build via `tooling/` + `@webex/legacy-tools` (Babel); type-check via `config/tsconfig.typecheck.json`.
- Test frameworks: Jest (unit), Mocha + Karma (integration/browser); lint via ESLint + Prettier.

## Architecture
```
Consumer app
   │  import Webex from 'webex'
   ▼
webex (unified) ── requires ──▶ @webex/webex-core (foundation)
                                   ├─ plugin registration + AmpersandState tree
                                   ├─ HTTP interceptor pipeline ──▶ Webex services
                                   ├─ credentials / services (auth + discovery)
                                   └─ storage adapters + config + events
   ▲
   └── @webex/plugin-* and @webex/internal-plugin-* register onto core
```
→ Full repo architecture & component responsibilities: **[ARCHITECTURE.md](./ai-docs/ARCHITECTURE.md)**

## Module / Package Structure
```
packages/
  webex/                     # unified SDK entry (requires public plugins)
  @webex/webex-core/         # TRACKED — plugin/HTTP/auth/storage/config/event foundation
  @webex/plugin-*/           # public plugins (people, rooms, meetings, messages, …) [Untracked]
  @webex/internal-plugin-*/  # internal plugins (device, mercury, locus, …) [Untracked]
  @webex/storage-adapter-*/  # storage backends (local-storage, session, local-forage) [Untracked]
  tools/ , config/ , legacy/ # build tooling & shared config [Untracked]
```
→ Per-module docs and the spec router: **[ai-docs/SPEC_INDEX.md](./ai-docs/SPEC_INDEX.md)**

## Critical Rules
1. **Code is the source of truth.** Never invent an API, path, event, flag, or constant — read the real file. Only `@webex/webex-core` has a canonical spec today; for every other package, cross-check code.
2. **Ask before coding.** Present a plan / Spec Summary; wait for confirmation.
3. **Plugins register through core.** New plugin behavior goes through `registerPlugin`/`registerInternalPlugin` (`packages/@webex/webex-core/src/webex-core.js`); do not bypass the plugin/interceptor model.
4. **Never commit or log tokens/credentials.** Auth flows through `lib/credentials` and the `AuthInterceptor`; see `ai-docs/SECURITY.md`.
5. **Each package builds independently.** Use the per-package `build:src`/`test:*` scripts via Yarn workspaces; do not assume a single root build covers a package.

## Essential Commands
| Role | Command |
|---|---|
| Install | `yarn install` |
| Build | `yarn build` |
| Unit test | `yarn test:unit` |
| Lint/format | `yarn lint` |
→ Full command surface, toolchain, and registries: **[GETTING_STARTED.md](./ai-docs/GETTING_STARTED.md)** · test tiers & coverage gate: **[TEST_INDEX.md](./ai-docs/TEST_INDEX.md)**

## Common Gotchas
1. Workspace protocol: intra-repo deps use `workspace:*`; running a package in isolation without `yarn install` at the root breaks resolution.
2. Browser vs Node: many files have `.shim.js` browser overrides (`package.json` `browser` map); editing the Node file alone can silently diverge browser behavior.
3. `Services` lives inside `webex-core` (not a standalone internal plugin) on purpose — it must initialize before credentials or early requests fail federation (`packages/@webex/webex-core/src/index.js` header comment).

## Pre-Commit Checklist
- [ ] Tests pass; coverage meets the repo bar
- [ ] Spec/docs updated in the same change (spec-currency) — including the `webex-core` spec if you touched it
- [ ] No hardcoded secrets; inputs validated
- [ ] Public-surface changes mirrored in `ai-docs/CONTRACTS.md` and `.sdd/manifest.json`

---
**SDD coverage:** this repo's per-module coverage state lives in `.sdd/manifest.json` (mirror in
`ai-docs/SPEC_INDEX.md`). Use that state to decide whether the spec is authoritative or code must be
cross-checked. This is an **assess-only** onboarding draft; canonical artifacts are non-authoritative
until validated by an independent runtime (host-owned).
