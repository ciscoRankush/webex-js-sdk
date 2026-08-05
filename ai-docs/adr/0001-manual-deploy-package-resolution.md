# ADR 0001 — Manual-deploy publishable-package resolution

- **Status:** Accepted (historical; imported during SDD bootstrap)
- **Date:** 2026-06-11
- **Scope:** `.github/workflows/manual-deploy.yml` CI publish pipeline
- **Source:** migrated by reference from `docs/superpowers/specs/2026-06-11-manual-deploy-package-resolution-design.md` (retained in place as the full design record)

## Context

`.github/workflows/manual-deploy.yml` resolved the set of publishable packages **three different ways** across three jobs (version-set in `publish-npm` and `publish-documentation`, NPM deploy, and changelog), with no shared output, so the three views could drift silently. The automated `deploy.yml` already computes the package list once in a `generate-package-matrix` job and exposes it as job outputs; manual deploy did not mirror that shape. Concrete issues included a duplicated list computation, a no-op `tr '\n' ' ' | sed` pipe, and a changelog step that depended on **bash** brace expansion (so quoting it would silently break the step).

## Decision

Make the `build` job the single source of truth for the publishable-package list:

- Add one `yarn package-tools list --mode node` step (`build.list`) that emits a clean **space-separated** list (no brace syntax), guarded against empty output, exposed as `build.outputs.packages`.
- All three downstream operations consume `needs.build.outputs.packages`:
  - `publish-npm` version-set and deploy via `yarn workspaces foreach --from "$PACKAGES" …`;
  - `publish-documentation` version-set via the same `--from "$PACKAGES"`;
  - `publish-documentation` changelog via `yarn package-tools changelog --packages $PACKAGES …` (intentionally unquoted variadic args, matching `deploy.yml`).
- Remove the per-job "get packages" steps, the `tr | sed` no-op, and the `--all --no-private` filters on the version-set steps.

## Consequences

- One authoritative list; version-set, NPM publish, and changelog can no longer diverge.
- `--mode node` output is safe whether interpolated quoted or unquoted; no dependence on shell brace expansion.
- Empty-list guard fails the build early rather than silently skipping publish.

## Out of Scope

Token scoping / action-SHA pinning / actor allowlist (handled elsewhere), verifying all non-private packages use `workspace:*` for internal deps, and consolidating the two duplicated version-set steps into a composite action.

> This ADR summarizes the decision for navigation. The complete design, YAML snippets, data-flow diagrams, and testing approach remain in the retained source document under `docs/superpowers/specs/`.
