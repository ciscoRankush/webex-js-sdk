<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: spec-index@0.2.1
generated_by: claude-cli
approved_by: pending
updated_at: 2026-07-31T00:00:00Z
validation_status: not-run
-->
# Spec Index — Webex JS SDK (authorization plugin family)

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry). This file is the router (generated at `ai-docs/SPEC_INDEX.md`); system overview in [`ARCHITECTURE.md`](ARCHITECTURE.md). Load `AGENTS.md` + this file first; pull every other doc on demand.
> Context-efficiency: link to canonical docs — don't duplicate them; route to the minimum needed per task.

> AI agent entry point after `AGENTS.md`. Load this once at session start; pull other docs on demand.
> **Source of truth:** `.sdd/manifest.json` (this file mirrors it for humans).

## Module Registry

| Module | Responsibility | Manifest coverage state | Start here |
|---|---|---|---|
| `packages/@webex/plugin-authorization/` | Environment auto-loader; re-exports the browser or node authorization implementation | Untracked | `packages/@webex/plugin-authorization/ai-docs/plugin-authorization-spec.md` |
| `packages/@webex/plugin-authorization-browser/` | Public browser OAuth2 (Implicit + Authorization Code) + JWT guest login | Untracked | `packages/@webex/plugin-authorization-browser/ai-docs/plugin-authorization-browser-spec.md` |
| `packages/@webex/plugin-authorization-node/` | Server-side OAuth2 code exchange + JWT | Untracked | `packages/@webex/plugin-authorization-node/ai-docs/plugin-authorization-node-spec.md` |
| `packages/@webex/plugin-authorization-browser-first-party/` | Hardened PKCE + QR device login for the Webex web client | Untracked | `packages/@webex/plugin-authorization-browser-first-party/ai-docs/plugin-authorization-browser-first-party-spec.md` |

## Task Routing

| If the task is… | Load |
|---|---|
| Understanding the system | `ARCHITECTURE.md` |
| Working in a specific plugin | that module's spec (see registry) |
| A public-surface / contract change | `CONTRACTS.md` + the owning module spec |
| Security-sensitive auth change | `SECURITY.md` + the owning module spec |
| Running or changing tests | `TEST_INDEX.md` + the affected module spec |
| Updating docs after a code change | affected module specs + relevant standing indexes/contracts |
| Migrating existing specs | manifest source routes + affected module specs + source-fidelity report |

## Spec Registry

| Doc | Location | Purpose |
|---|---|---|
| Rules | `RULES.md` | enforceable do/don't beyond AGENTS.md critical rules |
| Glossary | `GLOSSARY.md` | ubiquitous language: term → definition → code location |
| Security | `SECURITY.md` | trust boundaries, OAuth/CSRF/PKCE, token handling |
| Contracts | `CONTRACTS.md` | root index of public-surface contracts; details at owning modules |
| Service state | `SERVICE_STATE.md` | living as-built registry of surfaces and dependencies |
| Test index | `TEST_INDEX.md` | test tiers, canonical commands, locations, frameworks |
| Getting started | `GETTING_STARTED.md` | clone/build/run + monorepo workspace layout |
| Architecture | `ARCHITECTURE.md` | system shape, components, interactions, security architecture |
| Review catalog | `REVIEW_CHECKLIST.md` | the 6-core + 4-coverage + 3-cross-cutting review checks |
