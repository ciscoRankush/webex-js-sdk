<!-- sdd-generated-metadata
doc_kind: standing-doc
generated_from: rules@0.2.2
generated_by: claude-cli
approved_by: pending
updated_at: 2026-08-05T00:00:00Z
validation_status: not-run
-->

# RULES — webex-js-sdk

> Start here → root [`AGENTS.md`](../AGENTS.md) (agent entry) · router [`SPEC_INDEX.md`](SPEC_INDEX.md).
> Enforceable conventions extracted from the repository's real developer guidance (root `AGENTS.md`) and package-level agent docs. Constraints here bind every change; detailed commands live in [`GETTING_STARTED.md`](GETTING_STARTED.md).

## Scope & Workflow Rules

- **RULE-1 — Single-plugin focus.** Work in one package at a time. Build and test only the target package's workspace; do not broaden a change across unrelated plugins. (Source: root `AGENTS.md` "Code structure".)
- **RULE-2 — Code is the referee.** Never invent APIs, paths, events, flags, or constants; read the real source file. Legacy AI docs are migration inputs until independently validated.
- **RULE-3 — Ask before coding.** Present the affected files/contracts and a plan, then wait for confirmation before implementing.

## Refactoring Rules

Extracted from the root `AGENTS.md` "Refactoring guidelines". When asked to refactor, encapsulate, or redesign a method/API, do upfront design thinking **before** writing code:

- **RULE-4 — Signature first.** Reason about the ideal method signature after the change before touching call sites.
- **RULE-5 — Fix the call sites.** Reason about what each call site should look like; if logic moves inside a method, the caller must no longer need to know about it.
- **RULE-6 — Let the type system enforce correctness.** Consider whether TypeScript types (e.g. discriminated unions) can prevent invalid argument combinations.
- **RULE-7 — No half-steps.** Implement from the signature design, not from a minimal diff; follow the request to its logical conclusion in a single pass.

## Pattern-Search Rules

Extracted from the root `AGENTS.md` "Searching for patterns" guidance. When asked to find all occurrences of a logical pattern (e.g. "where state is LEFT and reason is MOVED"):

- **RULE-8 — Search both forms.** Search for **both** named constants (e.g. `_LEFT_`, `_MOVED_`) **and** raw string literals (`'LEFT'`, `'MOVED'`, `"LEFT"`, `"MOVED"`); the codebase is inconsistent between imported constants and inline strings.
- **RULE-9 — Include related enums.** Also search related enum values (e.g. `LOCUS.STATE.*`, `MEETING_STATE.STATES.*`) that represent the same logical concept.
- **RULE-10 — Confirm the full set.** After finding all matches, list every occurrence and confirm the full set with the requester before making changes.

## Testing Rules

Extracted from the root `AGENTS.md` "Test writing guidelines" and `plugin-meetings` package guidance. See [`GETTING_STARTED.md`](GETTING_STARTED.md) for the full list.

- **RULE-11 — Match existing style.** Follow the existing tests' coding style; use `sinon` for mocks/stubs and `assert` from `@webex/test-helper-chai`.
- **RULE-12 — Prefer precise assertions.** Use `assert.calledOnceWithExactly` instead of separate `calledOnce`/`calledWith` calls; use `sinon.useFakeTimers()` to control time.
- **RULE-13 — Parametrize and reuse.** When there are more than 3 similar cases, parametrize; reuse or extend helper methods rather than duplicating test code.
- **RULE-14 — Targeted slow suites.** For slow suites (e.g. `plugin-meetings`), temporarily add `.only` to iterate and always remove it before commit.

## Security & Currency Rules

- **RULE-15 — Never log secrets.** Do not print, log, or persist tokens/credentials; the auth interceptor attaches bearer tokens automatically.
- **RULE-16 — Spec-currency.** Update the owning package/module spec and standing catalogs in the same change as any behavior, public-surface, event, or contract change.

## Notes on Provenance

These rules were extracted from the repository's committed developer guidance during assess-only SDD bootstrap. Package-specific rules additionally live in each package's own SDD tree (e.g. `packages/calling/ai-docs/`, `packages/@webex/contact-center/ai-docs/`). This standing doc does not replace those; it captures the repository-wide conventions. Additional conventions from a 10–15 file code sweep are pending a rigorous pass and marked here as an explicit gap: [NEEDS HUMAN INPUT] — confirm repo-wide import/error/format idioms beyond the migrated guidance.
