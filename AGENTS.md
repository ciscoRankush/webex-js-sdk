<!-- sdd-generated-metadata
doc_kind: agent-entry
generated_from: agents@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->
# AGENTS.md — webex-js-sdk

> You are the agent entry point — read first. Next: router [`SPEC_INDEX.md`](ai-docs/SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ai-docs/ARCHITECTURE.md). Load this + `SPEC_INDEX.md` first; pull module/standing docs on demand. (Multi-repo: a workspace-level `AGENTS.md` may sit above this one.)
> Context-efficiency: link to canonical docs — don't duplicate them; keep this file under ~200 lines.

> Cross-tool context file. Auto-loaded by AI coding agents. If a module ships its own
> `AGENTS.md` agent-entry file, that file layers on top of this root file. (A module's
> high-level design lives in the manifest-routed module spec, source-local as `<module-path>/ai-docs/<module-name>-spec.md` by default, not in an `AGENTS.md`.)

> **Assess-only draft.** This SDD tree was generated in assess-only mode for the `webex-js-sdk`
> monorepo. It surfaces what is specced and what is not; it gates nothing. Code remains the source of
> truth. Confirmations that had no committed evidence and no human answer were resolved with the
> automated `config-fallback` policy and are **not human-verified** — see
> `.generated/sdd/bootstrap-questionnaire.md`.

## Repo Overview
**webex-js-sdk** is a Yarn-workspaces mono-repository of the publicly published JavaScript packages
for Cisco's Webex platform: the unified `webex` SDK plus the `@webex/*` plugin, core, transport,
storage, and tooling packages that compose it (`package.json` workspaces, `README.md`).

**What it is:**
- A mono-repo of npm-published JS/TS SDK packages that wrap the Webex REST and realtime APIs.
- A plugin architecture: `@webex/webex-core` provides the plugin host, HTTP request pipeline,
  credentials/auth, storage, config, and event framework; feature plugins layer on top.
**What it is NOT:**
- ❌ A Webex backend service or API server — it is a client SDK consumed by other applications.
- ❌ The owner of Webex domain data — remote Webex services own that; the SDK caches/transports it.

## Tech Stack
- JavaScript + TypeScript, Node 18.x, Yarn 3 (Berry) workspaces; Babel + `@webex/*-tools` build wiring.
- Test frameworks: Jest (unit), Karma/Mocha + WebdriverIO (integration/browser); ESLint + Prettier.

## Architecture
```
consumer app → webex (unified SDK) → @webex/webex-core (plugin host)
                                        ├── HTTP request pipeline (interceptor chain)
                                        ├── credentials / auth
                                        ├── storage adapters (bounded / unbounded)
                                        └── public + internal feature plugins (meetings, people, mercury, …)
```
→ Full repo architecture & component responsibilities: **[ARCHITECTURE.md](./ai-docs/ARCHITECTURE.md)**

## Module / Package Structure
```
packages/webex/                    # unified public SDK entry (webex.init)
packages/@webex/webex-core/        # plugin host, HTTP pipeline, credentials, storage, config, events
packages/@webex/plugin-*/          # public feature plugins (meetings, people, rooms, messages, …)
packages/@webex/internal-plugin-*/ # internal plugins (device, mercury, metrics, …)
packages/@webex/*-tools, config/*  # build/lint/test tooling and shared config
```
→ Per-module docs and the spec router: **[ai-docs/SPEC_INDEX.md](./ai-docs/SPEC_INDEX.md)**

## Critical Rules
1. **Code is the source of truth.** Never invent an API, path, event, flag, or constant — read the real file. Only `@webex/webex-core` has a canonical spec today; every other package is code-first.
2. **Ask before coding.** Present a plan / Spec Summary; wait for confirmation.
3. **Never overwrite an existing spec or standing doc in place.** Route changes through the module spec and manifest; the routed legacy doc `webex-plugin-architecture.md` is `retain` (kept, not superseded).
4. **Never commit or log tokens/credentials.** Auth tokens flow through `@webex/webex-core` credentials; treat them as secrets (see `ai-docs/SECURITY.md`).

## Essential Commands
| Role | Command |
|---|---|
| Install | `yarn install` |
| Build | `yarn build` |
| Unit test | `yarn test:unit` |
| Lint/format | `yarn lint` |
→ Full command surface, toolchain, and registries: **[GETTING_STARTED.md](./ai-docs/GETTING_STARTED.md)** · test tiers & coverage gate: **[TEST_INDEX.md](./ai-docs/TEST_INDEX.md)**

## Common Gotchas
1. Credentials accept many input shapes (string token, `credentials.supertoken`, `access_token`, …) and are normalized in `WebexCore.constructor`; passing a token on the wrong path silently no-ops (`packages/@webex/webex-core/src/webex-core.js`).
2. Interceptor **order** matters — pre/core/post ordering in `webex-core.js` governs auth, timing, and retry; reordering can break 401 re-auth replay.
3. Workspace builds are topological; building a single package without its `@webex/*` workspace deps built first will fail (`package.json` build scripts).

## Pre-Commit Checklist
- [ ] Tests pass; coverage meets the repo bar
- [ ] Spec/docs updated in the same change (spec-currency)
- [ ] No hardcoded secrets; inputs validated
- [ ] `@webex/webex-core` public surface and interceptor ordering preserved unless the change is intentional and specced

---
**SDD coverage:** this repo's per-module coverage state lives in `.sdd/manifest.json` (mirror in
`ai-docs/SPEC_INDEX.md`). Use that state to decide whether the spec is authoritative or code must be cross-checked.
