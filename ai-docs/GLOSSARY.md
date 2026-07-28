<!-- sdd-generated-metadata
  generator: claude-sonnet-4-6
  approver: [NEEDS HUMAN INPUT]
  generated_at: 2026-07-28T00:00:00Z
  validation_status: draft-assess-only
  source_policy: migrate-existing
  source_material: webex-plugin-architecture.md, docs/labs/contact-center/README.md, packages/calling/src/, package.json
-->

# GLOSSARY.md — webex-js-sdk

> Domain terms and abbreviations used across the Webex JS SDK codebase.

## domain terms

| Term | Definition |
|---|---|
| **WebexCore** | The central plugin host class (`@webex/webex-core`). Manages plugin registration, HTTP pipeline, credentials, storage, and events for the main `webex` SDK. |
| **Plugin** | A capability module registered on `WebexCore` via `registerPlugin` (public) or `registerInternalPlugin` (internal). All legacy plugins extend `AmpersandState`. |
| **Mercury** | Webex's WebSocket-based push-event protocol. `internal-plugin-mercury` manages the connection, auth, and reconnect logic. |
| **Locus** | Webex's real-time meeting signaling service. `internal-plugin-locus` handles the Locus protocol for meeting state changes. |
| **KMS** | Key Management Service — provides and manages encryption keys for E2E encrypted Webex content. |
| **WDM** | Webex Device Manager — registers the SDK as a logical "device" so it can receive Mercury push events. |
| **CallingClient** | Main entry-point class in `@webex/calling` for initiating and managing PSTN/SIP calls via an XState finite-state machine. |
| **Task (Contact Center)** | Core Contact Center entity representing a customer interaction (inbound/outbound call, chat, etc.). |
| **Entry Point** | The phone number or routing resource where inbound customer contacts arrive in the Contact Center. |
| **Activity Threading** | The algorithm in `internal-plugin-conversation` that reorders chronological conversation activities into thread order (root activity followed immediately by its replies). |
| **Interceptor** | A request/response transform function in the `@webex/http-core` pipeline. Applied in a fixed order on every HTTP request/response. |

## abbreviations & acronyms

| Abbreviation | Expansion |
|---|---|
| **PAT** | Personal Access Token — a developer.webex.com token for testing; not for production use |
| **ROAP** | Real-time Object Access Protocol — SDP/ICE offer-answer negotiation used in `plugin-meetings` |
| **FSM** | Finite-State Machine — the XState-based call lifecycle model in `@webex/calling` |
| **USM** | Unified Service Meeting — the newer Webex meeting model keyed by `MEETING_ID` rather than room ID |
| **DSS** | Directory Search Service — people/contact directory search via `internal-plugin-dss` |
| **LLM** | Large Language Model — `internal-plugin-llm` integrates Webex AI assistant capabilities |
| **CSRF** | Cross-Site Request Forgery — the SDK generates and validates a `state` parameter on all browser OAuth redirects |
| **UMD** | Universal Module Definition — pre-built browser bundles in `packages/webex/umd/` for `<script>` tag usage |
| **CJS** | CommonJS — the module format used by legacy Babel-compiled packages (`require`/`module.exports`) |
| **ESM** | ECMAScript Modules — the module format used by modern TypeScript packages (`import`/`export`) |
| **ADR** | Architecture Decision Record — stored in `ai-docs/adr/` |
| **DRI** | Directly Responsible Individual — the person accountable for a document or process |
| **WS6** | Workspace 6 — Cisco-internal architecture review tier; referenced in `ai-docs/ARCHITECTURE.md` |
| **BYODS** | Bring Your Own Data Store — the `@webex/byods` SDK for custom data persistence |
| **SDD** | Spec Driven Development — the documentation methodology used by this repo's `ai-docs/` artifacts |

## maintenance

| Term | Definition |
|---|---|
| **WebexCore** | The central plugin host class (`@webex/webex-core`). Manages plugin registration, HTTP pipeline, credentials, storage, and events. |
| **Plugin** | A capability module registered on `WebexCore` via `registerPlugin` (public) or `registerInternalPlugin` (internal). Extends `AmpersandState`. |
| **registerPlugin** | API for registering a public-facing plugin; exposes the plugin on the `webex` instance surface. Evidence: `packages/@webex/webex-core/src/index.js` |
| **registerInternalPlugin** | API for registering an internal plugin; not exposed on the public `webex` surface. Evidence: `packages/@webex/webex-core/src/index.js` |
| **AmpersandState** | State management base class from `ampersand-state`; used as the plugin base class for legacy packages. |
| **Interceptor** | A request/response transform function in the HTTP pipeline. Registered with `WebexCore.interceptors`. |
| **transform / applyNamedTransform** | APIs for applying payload transforms bidirectionally (request encoding, response decoding) in `WebexCore`. |
| **Access Token** | OAuth 2.0 bearer token used to authenticate all Webex API calls. Short-lived; refreshed via the credentials system. |
| **Refresh Token** | Long-lived OAuth 2.0 token used to obtain a new access token when the current one expires. |
| **ready event** | `webex.once('ready', cb)` — fires after all plugins are initialized and the device is registered. |

## Authentication / Authorization Terms

| Term | Definition |
|---|---|
| **Implicit Grant** | OAuth 2.0 flow for public browser clients; response_type=token. Uses `clientType: 'public'` (default). |
| **Authorization Code Grant** | OAuth 2.0 flow for confidential clients (browser or Node); response_type=code, requires client secret. |
| **Client Credentials Flow** | Server-to-server OAuth flow without user context. Used for bot/service accounts. |
| **JWT Authentication** | Guest/bot server-side auth using a signed JWT exchanged for a Webex access token. |
| **Webex ID Broker** | Cisco's identity/OAuth service that issues authorization codes and tokens. |
| **CSRF** | Cross-Site Request Forgery protection built into the browser OAuth flow. |
| **clientType** | Configuration parameter: `'public'` triggers implicit grant; `'confidential'` triggers authorization code grant. |
| **PAT** | Personal Access Token — a development/testing token from developer.webex.com. Not for production. |

## Network / Protocol Terms

| Term | Definition |
|---|---|
| **Mercury** | Webex's WebSocket-based push event protocol. `internal-plugin-mercury` manages the connection. |
| **Locus** | Webex's meeting signaling service. `internal-plugin-locus` handles the Locus protocol. |
| **WDM** | Webex Device Manager — registers the SDK as a device to receive push events via Mercury. |
| **ROAP** | Real-time Object Access Protocol — SDP/ICE offer/answer negotiation used in plugin-meetings for WebRTC. |
| **KMS** | Key Management Service — provides encryption keys for E2E encrypted content. |
| **DSS** | Directory Search Service — people directory search via `internal-plugin-dss`. |
| **LLM** | Large Language Model — `internal-plugin-llm` integrates Webex AI assistant capabilities. |

## Calling SDK Terms

*Evidence: `packages/calling/src/` directory*

| Term | Definition |
|---|---|
| **CallingClient** | Main calling entry point class in `@webex/calling`. Manages calls via XState FSM. |
| **CallHistory** | Stores and retrieves call history records. `packages/calling/src/CallHistory/` |
| **CallSettings** | Per-user calling configuration (voicemail, do-not-disturb, etc.). `packages/calling/src/CallSettings/` |
| **Voicemail** | Voicemail retrieval and management. `packages/calling/src/Voicemail/` |
| **Contacts** | Contact directory for calling. `packages/calling/src/Contacts/` |
| **SDKConnector** | Connects `@webex/calling` to the core Webex SDK instance. `packages/calling/src/SDKConnector/` |
| **USM** | Unified Service Meeting — the newer meeting model where meetings are created with `MEETING_ID` rather than room IDs. |
| **XState** | State machine library (v4) used in `@webex/calling` for call lifecycle management. |

## Contact Center Terms

*Evidence: `docs/labs/contact-center/README.md`, `packages/@webex/contact-center/typedoc.md`*

| Term | Definition |
|---|---|
| **Agent Profile** | Data returned after `cc.register()` — teams, login voice options, agent states, auxiliary codes, wrapup codes. |
| **Station** | The agent's workstation configuration (Browser WebRTC, Agent DN, or Extension). |
| **Device ID** | Unique identifier for a logged-in station; required for `stationLogout`. |
| **Auxiliary Code** | Reason for an agent being in not-ready state (e.g., Lunch, Training). |
| **Wrapup Code** | Reason code recorded at task completion (e.g., RESOLVED). |
| **Task** | Core Contact Center entity representing an interaction (call, chat, etc.). |
| **Entry Point** | Phone number or routing resource where inbound customer contacts arrive. |
| **task:incoming** | Event emitted when a new task is offered to the agent. |
| **task:hydrate** | Event emitted when task state is restored after page refresh. |
| **task:assigned** | Event emitted when a task is successfully assigned to the agent. |

## Build / Tooling Terms

| Term | Definition |
|---|---|
| **webex-legacy-tools** | Internal build toolchain (`@webex/legacy-tools`) used for Babel-based legacy package builds. |
| **webex-package-tools** | Internal toolchain (`@webex/package-tools`) for workspace package management. |
| **Topological build** | Build order that respects workspace dependency edges — a package builds only after all its dependencies are built. |
| **UMD bundle** | Universal Module Definition build output (`packages/webex/umd/`) for direct `<script>` tag usage. |
| **devMain** | Custom `package.json` field pointing to the un-built source entry (`src/index.js`) for local development. |
| **Workspace protocol** | Yarn 3 `workspace:*` reference in `package.json` for cross-package local dependencies. |
| **Artifactory** | Cisco's internal npm registry at `engci-maven-master.cisco.com/artifactory/api/npm/webex-release-npm`. |

## Conversation Terms

*Evidence: `packages/@webex/internal-plugin-conversation/src/activity-threading.md`*

| Term | Definition |
|---|---|
| **Activity Threading** | The algorithm that reorders chronological conversation service activities into thread order (root + replies grouped). |
| **Thread Root** | A top-level activity in a conversation (the "parent" of replies). |
| **Thread Reply** | A child activity that belongs to a thread root. |
| **Thread Order** | Activities sorted as: newest-root-first, with each root's replies immediately following it (not chronological order). |

## maintenance

- Add new terms when a domain concept appears in two or more module specs without being defined here.
- Update entries when a term's canonical implementation location moves or a public API is renamed.
- Remove entries for deprecated terms only after the deprecated code is removed from all packages.
- **Owner:** [NEEDS HUMAN INPUT] — Assign a DRI responsible for keeping this glossary current.
