<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: spec-index@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->

# SPEC_INDEX — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · system [`ARCHITECTURE.md`](ARCHITECTURE.md) · surfaces [`CONTRACTS.md`](CONTRACTS.md).
> Human-readable mirror of `.sdd/manifest.json` `modules[]`. Load only the module spec you need.

## Standing Docs

| Doc | Path |
|---|---|
| Agent entry | [`AGENTS.md`](../AGENTS.md) |
| Architecture | [`ARCHITECTURE.md`](ARCHITECTURE.md) |
| Getting started | [`GETTING_STARTED.md`](GETTING_STARTED.md) |
| Rules | [`RULES.md`](RULES.md) |
| Contracts | [`CONTRACTS.md`](CONTRACTS.md) |
| Decisions | [`adr/`](adr/) |

## Module Registry

| Module | Coverage state | Doc kind | Start here |
|---|---|---|---|
| `packages/@webex/webex-core` | Untracked | Module spec | [`packages/@webex/webex-core/ai-docs/webex-core-spec.md`](../packages/@webex/webex-core/ai-docs/webex-core-spec.md) |
| `packages/calling` | Partial | Module spec | [`packages/calling/ai-docs/calling-package-spec.md`](../packages/calling/ai-docs/calling-package-spec.md) |
| `packages/@webex/contact-center` | Partial | Module spec | [`packages/@webex/contact-center/ai-docs/contact-center-package-spec.md`](../packages/@webex/contact-center/ai-docs/contact-center-package-spec.md) |
| `packages/@webex/internal-plugin-call-ai-summary` | Partial | Module spec | [`packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md`](../packages/@webex/internal-plugin-call-ai-summary/ai-docs/call-ai-summary-spec.md) |
| `packages/@webex/plugin-meetings` | Untracked | Module spec | [`packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md`](../packages/@webex/plugin-meetings/ai-docs/plugin-meetings-spec.md) |
| `cc_playwright` | Partial | Module spec | [`cc_playwright/ai-docs/e2e-testing-spec.md`](../cc_playwright/ai-docs/e2e-testing-spec.md) |

## Package-Local SDD Trees

Two capability packages maintain their own complete `.sdd` trees and remain authoritative within their package. The root registry above routes to an aggregating package spec; descend into the package tree for per-submodule detail:

- `packages/calling/.sdd/manifest.json` — per-submodule specs for CallHistory, CallRecording, CallSettings, CallingClient (+ calling/CallerId/line/registration), Contacts, Metrics, SDKConnector, Voicemail, mobius-socket.
- `packages/@webex/contact-center/.sdd/manifest.json` — per-submodule specs for metrics, services (+ agent/config/core/task/task-state-machine), utils.

## Coverage / Completeness Gap (assess-only)

This is an assess-only root bootstrap. The workspace contains many additional `@webex/*` plugins (people, messages, rooms, device, mercury, metrics, encryption, authorization, and others) that are **not yet routed** to canonical root specs. Routing the full plugin set is deferred to a rigorous pass. [NEEDS HUMAN INPUT] — confirm which additional plugins should be promoted to root-level module specs.
