<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
  spec_index: ai-docs/SPEC_INDEX.md
-->

# AGENTS.md — webex-js-sdk

> You are the agent entry point — read first. Next: router [`SPEC_INDEX.md`](ai-docs/SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ai-docs/ARCHITECTURE.md). Load this + `SPEC_INDEX.md` first; pull module/standing docs on demand.

## Repo Overview

**webex-js-sdk** is the official Cisco Webex JavaScript SDK — a Yarn workspace monorepo that provides browser and Node.js packages for building applications on the Webex platform, including messaging, meetings, calling, authorization, contact-center, and device management.

**What it is:**
- A Yarn 3 monorepo (`workspaces` in `package.json`) of ~74 publishable and internal packages under `packages/@webex/*`, `packages/webex`, `packages/webex-node`, `packages/calling`, `packages/byods`, and `packages/contact-center`
- The canonical JavaScript/TypeScript SDK consumed by Cisco Webex app developers
- A plugin-based architecture where `@webex/webex-core` is the foundational plugin host and all feature packages register as plugins

**What it is NOT:**
- ❌ Not a Webex backend service or API — it is a client SDK
- ❌ Not the Webex desktop/mobile application
- ❌ Not a standalone CLI tool

## Tech Stack

- **Language:** JavaScript (Babel) + TypeScript 4.9.5, Node 18.x minimum
- **Package manager:** Yarn 3.4.1 (node-modules linker, workspaces)
- **Build tooling:** `@webex/legacy-tools` (`webex-legacy-tools build`), webpack 5, tsc (declaration emit), rollup (calling/byods)
- **Test:** Jest 29 (unit, global 85% coverage threshold), Mocha (integration), Karma (browser), WebdriverIO (e2e/samples)
- **Lint:** ESLint 8 (airbnb-base + custom `eslintrules/` modules), commitlint (conventional commits)
- **Frameworks:** AmpersandJS (legacy plugin state), XState (calling FSM), WebRTC Adapter

## Architecture

```
Consumer App
    │
    ▼
packages/webex (SDK entry — webex npm package)
    │
    ├─── @webex/webex-core          ← plugin registry, HTTP pipeline, credentials, storage, events
    │        │
    │        ├─── @webex/http-core  ← low-level HTTP request/response, interceptors
    │        └─── @webex/common     ← shared utilities
    │
    ├─── Public Plugins (registerPlugin)
    │    plugin-meetings, plugin-authorization, plugin-authorization-browser,
    │    plugin-authorization-node, plugin-encryption, plugin-messages,
    │    plugin-rooms, plugin-memberships, plugin-people, plugin-teams,
    │    plugin-presence, plugin-webhooks, plugin-attachment-actions,
    │    plugin-device-manager, plugin-logger
    │
    ├─── Internal Plugins (registerInternalPlugin)
    │    internal-plugin-mercury (WebSocket), internal-plugin-device,
    │    internal-plugin-conversation, internal-plugin-metrics,
    │    internal-plugin-encryption, internal-plugin-feature,
    │    internal-plugin-locus, internal-plugin-calendar, internal-plugin-dss,
    │    internal-plugin-llm, internal-plugin-presence, internal-plugin-mercury,
    │    internal-plugin-scheduler, internal-plugin-search, internal-plugin-support,
    │    internal-plugin-voicea, internal-plugin-wdm, ...
    │
    └─── Standalone SDK Packages
         @webex/calling (Calling SDK, XState FSM, typedoc)
         @webex/contact-center (Contact Center SDK)
         @webex/byods (Bring Your Own Data Store)
```

→ Full repo architecture & component responsibilities: **[ARCHITECTURE.md](./ai-docs/ARCHITECTURE.md)**

## Module / Package Structure

```
packages/
  webex/              — main `webex` npm package (SDK entry, browser + Node)
  webex-node/         — Node.js-specific SDK entry
  calling/            — @webex/calling — Calling SDK (TypeScript, XState)
  byods/              — @webex/byods — Bring Your Own Data Store SDK
  byods-demo-server/  — demo server for byods
  @webex/
    webex-core/               — plugin host, HTTP pipeline, credentials, storage
    http-core/                — HTTP request/response core
    common/                   — shared utilities
    common-evented/           — event mixin utilities
    common-timers/            — timer utilities
    contact-center/           — Contact Center SDK (@webex/contact-center)
    plugin-meetings/          — Meetings SDK (largest plugin, ~26 subsystems)
    plugin-authorization/     — OAuth flow orchestration
    plugin-authorization-browser/  — Browser OAuth (implicit + authcode grant)
    plugin-authorization-node/     — Node.js OAuth (authcode, JWT, client creds)
    plugin-encryption/        — E2E encryption (KMS, cypher)
    plugin-messages/          — Messaging API
    plugin-rooms/             — Spaces/Rooms API
    plugin-memberships/       — Room membership API
    plugin-people/            — People/identity API
    plugin-presence/          — Presence status API
    plugin-teams/             — Teams API
    plugin-logger/            — Logging plugin
    internal-plugin-mercury/  — WebSocket connection (Mercury protocol)
    internal-plugin-device/   — Device registration and management
    internal-plugin-conversation/ — Conversation service (activity threading)
    internal-plugin-metrics/  — Telemetry and metrics
    internal-plugin-locus/    — Locus (meeting signaling)
    internal-plugin-llm/      — LLM/AI assistant integration
    internal-plugin-feature/  — Feature flags
    ... (50+ additional packages)
  config/             — Shared ESLint, Jest, TypeScript, API-extractor config
  legacy/             — Legacy build tools and configs
  tools/              — CLI tools (@webex/cli-tools, @webex/package-tools)
```

→ Per-module docs and the spec router: **[ai-docs/SPEC_INDEX.md](./ai-docs/SPEC_INDEX.md)**

## Critical Rules

1. **Code is the source of truth.** Never invent an API, path, event, flag, or constant — read the real file first (`packages/@webex/<module>/src/`).
2. **Ask before coding.** Present a plan; wait for confirmation before making multi-file changes.
3. **Plugin registration pattern is mandatory.** All public plugins use `registerPlugin`; all internal plugins use `registerInternalPlugin` from `@webex/webex-core`. Never bypass this. Evidence: `packages/@webex/internal-plugin-mercury/src/index.js`.
4. **camelCase everywhere.** ESLint `camelcase: error` is enforced. Snake_case identifiers (from API responses) must be remapped at the boundary, not threaded through internal code. Evidence: `eslintrules/style.js`.
5. **No implicit equality.** `eqeqeq: [error, always]` — always use `===`/`!==`. Evidence: `eslintrules/best-practices.js`.
6. **Conventional commit messages are required.** Types: `build|chore|ci|docs|feat|fix|perf|refactor|revert|test`. Enforced via commitlint. Evidence: `commitlint.config.cjs`.
7. **Coverage floor is 85% globally.** Per-package Jest thresholds apply; some modules have lower approved exceptions (e.g., Hooks 50%, SDKConnector 60%). Never lower a threshold without explicit approval. Evidence: `jest.config.js`.
8. **TypeScript declarations only.** `tsconfig.json` uses `emitDeclarationOnly: true` — Babel handles transpilation. Never configure tsc for full emit on existing legacy packages. Evidence: `tsconfig.json`.

## Essential Commands

| Task | Command | Source |
|---|---|---|
| Install | `yarn install` | `package.json` |
| Build (local, all) | `yarn build:local` | `package.json` |
| Build (publish) | `yarn build:publish` | `package.json` |
| Build (tools first) | `yarn ws:tools` | `package.json` |
| Test (all) | `yarn test` | `package.json` |
| Test (unit, Node) | `yarn test:unit` | `package.json` |
| Test (integration) | `yarn test:integration` | `package.json` |
| Lint | `yarn lint` | `package.json` |
| Build docs | `yarn build:docs` | `package.json` |
| Samples serve | `yarn samples:serve` | `package.json` |
| Clean | `yarn clean` | `package.json` |

## Common Gotchas

- **Topological build order matters.** `yarn workspaces foreach --topological` must be respected; building a plugin before `@webex/webex-core` is built will fail silently at runtime. Evidence: `package.json` `build:local` script.
- **`webex-legacy-tools build`** is not `tsc` — it wraps Babel compilation with source maps. TypeScript-only packages (`calling`, `byods`) use plain `tsc` instead. Mixing them breaks declaration files.
- **Browser shims** — several packages have a `browser` field in `package.json` mapping `.js` → `.shim.js` for browser-incompatible Node APIs. If adding a Node-only dependency, add a shim entry too. Evidence: `packages/webex/package.json` `browser` field.
- **`internal-README.md`** in `plugin-meetings` is not the canonical spec — it is a developer note. Do not treat it as authoritative design documentation.
- **Dependency audit cadence.** Unmaintained packages must be triaged via `docs/dependency/audit-process.md` before any `ACCEPT` decision. Never silently keep a deprecated dependency.

## Always / Ask-First / Never

**Always:**
- Read the module's `src/index.js` (or `src/index.ts`) before proposing a change to understand its exported surface
- Run `yarn lint` before proposing any PR
- Verify the `test:unit` script passes after any change

**Ask-First:**
- Adding a new `peerDependency` or cross-workspace dependency edge
- Changing the `registerPlugin` / `registerInternalPlugin` registration for any module
- Lowering a Jest coverage threshold
- Modifying the `browser` shim map in any `package.json`

**Never:**
- Commit directly to `main`; always use a feature branch + PR
- Skip `commitlint` — all commits must be conventional format
- Publish a package without running `build:publish` first
- Add a `devDependency` that is already declared in the root `package.json` to an individual package (causes version drift)

## pre-commit checklist

Before committing or opening a PR, verify all of the following:

- [ ] `yarn lint` passes with no errors (ESLint camelCase, eqeqeq, curly, etc.)
- [ ] `yarn test:unit` passes for all changed packages
- [ ] Coverage thresholds are not lowered (global floor: 85% lines/functions/branches/statements)
- [ ] Commit message follows conventional format: `<type>(<scope>): <subject>` (enforced by commitlint)
- [ ] No secrets, tokens, or credentials in staged files (gitleaks runs in CodeRabbit on PR)
- [ ] New public exports have JSDoc/TSDoc annotations
- [ ] Any new cross-workspace dependency uses `workspace:*` protocol — not a version pin or relative path
- [ ] If adding a Node-only API to a browser-targeted package, a `.shim.js` + `browser` field entry is included
- [ ] If modifying plugin registration, both `registerPlugin` / `registerInternalPlugin` patterns are preserved
- [ ] Any new `package.json` dependency that is unmaintained/deprecated has a recorded triage decision in `docs/dependency/unmaintained-catalog.md`

## Routing Table

| Task | Load |
|---|---|
| Calling / telephony | `packages/calling/ai-docs/calling-spec.md` |
| Contact Center agent flows | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| OAuth / authorization | `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` |
| Browser auth | `packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md` |
| Node.js auth | `packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md` |
| Meetings / WebRTC | `packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md` [NEEDS HUMAN INPUT — not yet generated] |
| Encryption / KMS | `packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md` |
| WebSocket / Mercury | `packages/@webex/internal-plugin-mercury/ai-docs/internal-plugin-mercury-spec.md` [NEEDS HUMAN INPUT] |
| Conversation / messaging | `packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md` |
| Core plugin host | `packages/@webex/webex-core/ai-docs/webex-core-spec.md` [NEEDS HUMAN INPUT] |
| HTTP pipeline | `packages/@webex/http-core/ai-docs/http-core-spec.md` [NEEDS HUMAN INPUT] |
| Dependency governance | `docs/dependency/audit-process.md` |
| Coding rules | `ai-docs/RULES.md` |
| Architecture | `ai-docs/ARCHITECTURE.md` |
| All modules | `ai-docs/SPEC_INDEX.md` |
