<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
-->

# CONTRACTS.md — webex-js-sdk

> Repo-wide index of published package contracts, public API surfaces, and external service dependencies.
> No native OpenAPI/AsyncAPI/proto/GraphQL contract files were found in this repo at bootstrap time.
> Schema details live in TypeScript declaration files (`.d.ts`) generated under each package's `dist/types/`.

## Published npm Packages (Public Contracts)

| Package | npm name | Entry | Types |
|---|---|---|---|
| SDK (browser+Node) | `webex` | `dist/index.js` | via `@webex/webex-core` | [`packages/webex/ai-docs/webex-spec.md`](../packages/webex/ai-docs/webex-spec.md) |
| SDK (Node.js only) | `webex-node` | `dist/index.js` | via `@webex/webex-core` | [`packages/webex-node/ai-docs/webex-node-spec.md`](../packages/webex-node/ai-docs/webex-node-spec.md) |
| Calling SDK | `@webex/calling` | `dist/module/index.js` | `dist/types/index.d.ts` |
| Contact Center SDK | `@webex/contact-center` | `dist/webex.js` | `dist/types/index.d.ts` |
| BYODS SDK | `@webex/byods` | `dist/module/index.js` | `dist/types/index.d.ts` |

## Module Public Surfaces (Summary)

Detailed public surfaces are documented in each module's canonical spec. This index provides cross-module linkage.

| Module | Key Public API / Events | Canonical Spec |
|---|---|---|
| `webex-node` | `WebexNode.init()`, `WebexNode.version`; Node.js-only full-SDK entry | `packages/webex-node/ai-docs/webex-node-spec.md` |
| `@webex/webex-core` | `WebexCore` (SDK root), `registerPlugin()`, `registerInternalPlugin()`, `WebexPlugin`, `StatelessWebexPlugin`, `Credentials`, `Token`, `ServicesV2`, `Batcher`, `Page`, `WebexHttpError`, `makeWebexStore`, `MemoryStoreAdapter`, `@persist`, `@waitForValue`, all interceptors | [`packages/@webex/webex-core/ai-docs/webex-core-spec.md`](../packages/@webex/webex-core/ai-docs/webex-core-spec.md) |
| `@webex/http-core` | HTTP request pipeline, interceptor interface | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-authorization` | Environment-detection OAuth orchestration | `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` |
| `@webex/plugin-authorization-browser` | `initiateImplicitGrant()`, `initiateAuthorizationCodeGrant()`, `initiateLogin()`, browser token storage, `ready` event | `packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md` |
| `@webex/plugin-authorization-node` | Authorization Code Grant, JWT auth, client credentials, server-side token management | `packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md` |
| `@webex/plugin-meetings` | Meeting lifecycle: `create()`, `join()`, `addMedia()`, `verifyPassword()`, WebRTC, ROAP, breakouts, transcription | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-encryption` | `webex.cypher.register()`, `webex.cypher.downloadAndDecryptFile()`, KMS key management | `packages/@webex/plugin-encryption/ai-docs/plugin-encryption-spec.md` |
| `@webex/calling` | `CallingClient`, `CallHistory`, `CallSettings`, `Voicemail`, `Contacts`, USM meeting flow, XState FSM | `packages/calling/ai-docs/calling-spec.md` |
| `@webex/contact-center` | `cc.register()`, `cc.stationLogin()`, `cc.setAgentState()`, `cc.on('task:incoming')`, `task.accept()`, `task.wrapup()` | `packages/@webex/contact-center/ai-docs/contact-center-spec.md` |
| `@webex/internal-plugin-conversation` | Thread ordering API (`activity-threading`), conversation service client | `packages/@webex/internal-plugin-conversation/ai-docs/internal-plugin-conversation-spec.md` |
| `@webex/internal-plugin-mercury` | WebSocket connect/disconnect, event routing, `mercury.connect()`, `mercury.logout()` | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-messages` | `webex.messages.create()`, `list()`, `get()`, `remove()` | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-rooms` | `webex.rooms.create()`, `list()`, `get()`, `update()`, `remove()` | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-people` | `webex.people.get()`, `list()`, `getMe()` | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-memberships` | `webex.memberships.create()`, `list()`, `remove()` | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/plugin-webhooks` | `webex.webhooks.create()`, `list()`, `remove()` | [NEEDS HUMAN INPUT — spec pending] |
| `@webex/byods` | BYODS data store interface, `jose`-based JWT, `node-fetch` HTTP | [NEEDS HUMAN INPUT — spec pending] |

## External Service Dependencies

| Service | Protocol | Used by |
|---|---|---|
| Webex Identity Broker | HTTPS (OAuth 2.0) | `plugin-authorization-*` |
| Webex Device Manager (WDM) | HTTPS | `internal-plugin-device`, `internal-plugin-wdm` |
| Mercury WebSocket | WSS | `internal-plugin-mercury` |
| Locus (meeting signaling) | HTTPS + WSS | `internal-plugin-locus`, `plugin-meetings` |
| KMS (Key Management Service) | HTTPS | `plugin-encryption`, `internal-plugin-encryption` |
| Conversation Service | HTTPS | `internal-plugin-conversation`, `plugin-messages`, `plugin-rooms` |
| Webex Contact Center API | HTTPS + WSS | `@webex/contact-center` |
| Calling/Broadworks | HTTPS + WebRTC | `@webex/calling` |
| AI/LLM Service | HTTPS | `internal-plugin-llm` |
| Directory Search Service | HTTPS | `internal-plugin-dss` |
| Metrics/Telemetry | HTTPS | `internal-plugin-metrics` |

## Artifact Registry

- **Production publish:** `http://engci-maven-master.cisco.com/artifactory/api/npm/webex-release-npm`
- **Development resolve:** `https://registry.npmjs.org` (`.yarnrc.yml`)

## requires — what this repo depends on

The SDK depends on the following external Webex platform services at runtime (consumers must have network access to these):

| Service | Protocol | Required by |
|---|---|---|
| Webex Identity Broker (OAuth 2.0) | HTTPS | All auth flows (`plugin-authorization-*`) |
| Webex Device Manager (WDM) | HTTPS | `internal-plugin-device`, `internal-plugin-wdm` |
| Mercury WebSocket | WSS | `internal-plugin-mercury` (push events) |
| Locus (meeting signaling) | HTTPS + WSS | `internal-plugin-locus`, `plugin-meetings` |
| KMS (Key Management Service) | HTTPS | `plugin-encryption`, `internal-plugin-encryption` |
| Conversation Service | HTTPS | `internal-plugin-conversation`, `plugin-messages`, `plugin-rooms` |
| Webex Contact Center API | HTTPS + WSS | `@webex/contact-center` |
| Calling / Broadworks | HTTPS + WebRTC | `@webex/calling` |
| AI/LLM Service | HTTPS | `internal-plugin-llm` |
| Directory Search Service (DSS) | HTTPS | `internal-plugin-dss` |
| Metrics/Telemetry endpoint | HTTPS | `internal-plugin-metrics` |

**npm registry dependencies:**
- Production resolve: `https://registry.npmjs.org` (`.yarnrc.yml`)
- Publish registry: `http://engci-maven-master.cisco.com/artifactory/api/npm/webex-release-npm` (Cisco internal)

**Key pinned / version-constrained packages:**
- `jwt-decode@3.1.2` — pinned in `plugin-meetings` for Node 18 compatibility
- `jsdom@19.0.0` — pinned for browser-compatibility testing consistency
- `@webex/json-multistream@2.4.3` — resolved via root `resolutions` to prevent version drift

## compatibility & deprecation policy

- **Semver:** All published packages follow semantic versioning. Breaking changes require a major version bump.
- **Browser implicit grant (deprecated path):** The OAuth implicit grant flow (`clientType: 'public'`) is not recommended for new integrations. New applications should use the authorization code grant with PKCE. The implicit grant path remains supported for backwards compatibility but may be removed in a future major version.
- **AmpersandJS plugin base:** Legacy plugins extending `AmpersandState` are maintained but no new plugins should introduce AmpersandJS. Modern packages (`@webex/calling`, `@webex/contact-center`) use plain TypeScript classes.
- **`devMain` field:** The `devMain` convention in `package.json` is a legacy build artifact. New TypeScript packages use standard `main` / `exports` fields.
- **API stability:** Public contracts listed in the "Module Public Surfaces" table above are stable across minor versions. Internal plugin APIs (prefixed `internal-plugin-*`) are not part of the public contract and may change between minor versions.
- **Deprecation notices:** Deprecated APIs are annotated with `@deprecated` JSDoc/TSDoc tags in source and surfaced in TypeScript declaration files. Removal is announced in `CHANGELOG.md` and `UPGRADING.md` at least one major version in advance.

## maintenance

- **Contract artifacts** (TypeScript `.d.ts` files) are regenerated on every `build:publish` run. Do not hand-edit generated declaration files.
- **External service dependency list** in this document should be updated whenever a new internal plugin or standalone SDK adds a new service integration.
- **Module public surface table** should be updated when a module's canonical spec is created or materially changed.
- **Pinned dependencies** should be reviewed at each major Node.js LTS upgrade and whenever the pinning issue is resolved upstream.
- **Owner:** [NEEDS HUMAN INPUT] — Assign a DRI for keeping this document current as the SDK evolves.

## Native API Contract Sources

No OpenAPI, AsyncAPI, proto, or GraphQL schema files were found in this repo at bootstrap time. TypeScript declaration files (`dist/types/*.d.ts`) are the authoritative contract artifacts for published packages and are generated from source by `tsc --declaration`. Link to them from module specs rather than duplicating signatures in markdown.
