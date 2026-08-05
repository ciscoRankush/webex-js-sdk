<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: contracts@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->

# CONTRACTS — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) · router [`SPEC_INDEX.md`](SPEC_INDEX.md) · system [`ARCHITECTURE.md`](ARCHITECTURE.md).
> Compact repo-wide index of the public/consumed surfaces of documented packages. Exact type signatures live in each package's exported declarations and its canonical spec; this file routes, it does not paste full schemas.

## Public Package Exports & SDK Surfaces

| Contract ID | Type | Surface | Purpose | Detail |
|---|---|---|---|---|
| `webex-core.registerPlugin` | SDK | `registerPlugin(name, ctor, opts)` / `registerInternalPlugin(...)` | Register a public/internal plugin with the core | [`webex-core-spec.md`](../packages/@webex/webex-core/ai-docs/webex-core-spec.md) |
| `webex-core.request` | SDK | `webex.request(options)` | Execute an HTTP request through the interceptor pipeline | [`webex-core-spec.md`](../packages/@webex/webex-core/ai-docs/webex-core-spec.md) |
| `webex-core.storage` | SDK | `boundedStorage` / `unboundedStorage` `get/set/del/clear` | Namespaced client-side storage | [`webex-core-spec.md`](../packages/@webex/webex-core/ai-docs/webex-core-spec.md) |
| `calling.createClient` | SDK | `createClient(config) -> ICallingClient` | Create a calling client (lines, call control) | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| `calling.createCallHistoryClient` | SDK | `createCallHistoryClient(webex, logger) -> ICallHistory` | Call-history query/update/delete + events | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| `calling.createCallSettingsClient` | SDK | `createCallSettingsClient(webex, logger) -> ICallSettings` | Call waiting/DND/forwarding/voicemail settings | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| `calling.createContactsClient` | SDK | `createContactsClient(webex, logger) -> IContacts` | Contact/group CRUD | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| `calling.createVoicemailClient` | SDK | `createVoicemailClient(webex, logger) -> IVoicemail` | Voicemail list/content/state/transcript | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| `contact-center.ContactCenter` | SDK | `ContactCenter` WebexPlugin + `cc.*` methods/events | Agent/task/config/realtime CC surface | [`contact-center-package-spec.md`](../packages/@webex/contact-center/ai-docs/contact-center-package-spec.md) |
| `aisummary.getContainer` | SDK/event | `webex.internal.aisummary.getContainer({containerId})` | Resolve a Pragya container | [`call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| `aisummary.getSummary` | SDK | `getSummary({containerInfo}) -> SummaryContent` | Fetch+decrypt note/shortNote/actionItems | [`call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| `aisummary.getNotes` | SDK | `getNotes({containerInfo}) -> SummaryNotes` | Fetch+decrypt notes (standalone) | [`call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| `aisummary.getActionItems` | SDK | `getActionItems({containerInfo}) -> SummaryActionItems` | Fetch+decrypt action items (standalone) | [`call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| `aisummary.getTranscriptUrl` / `getTranscript` | SDK | `getTranscriptUrl({containerInfo}) -> string` / `getTranscript(...) -> TranscriptContent` | Transcript URL / decrypted transcript | [`call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| `plugin-meetings.*` | SDK | Meetings public plugin surface | Meetings capability (surface pending confirmation) | [`plugin-meetings-spec.md`](../packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md) |

## Consumed External Contracts

| Contract | Provider | Consumers | Detail |
|---|---|---|---|
| Locus / meetings APIs + events | Webex cloud | plugin-meetings | package spec |
| Mobius (call control) REST + Mercury WS | Webex cloud | calling | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| Janus (call history) REST + WS | Webex cloud | calling, call-ai-summary | package specs |
| XSI / Hydra / VMGateway | Webex cloud | calling (settings/voicemail) | [`calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| Pragya + AI Bridge | Webex cloud | call-ai-summary | [`call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| KMS (via internal-plugin-encryption) | Webex cloud | call-ai-summary, calling (contacts) | package specs |
| WCC REST + WebSocket (AQM) | Webex cloud | contact-center | [`contact-center-package-spec.md`](../packages/@webex/contact-center/ai-docs/contact-center-package-spec.md) |

> Coverage note: this index lists documented packages only. Undocumented `@webex/*` plugins expose additional public surfaces not yet indexed — see the SPEC_INDEX gap note. Native contract sources (exported `.d.ts` / package entry points) remain authoritative for exact names and types.
