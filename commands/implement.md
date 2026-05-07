# mde: Implement

Execute or finalize work for the active spec.

`implement` is an impact-aware adapter. It applies the confirmed work to the concrete artifacts required by the current spec.

---

## 1. Active Spec Resolution

`implement` operates on one existing spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, ask. If none exists, route to `specify`. Never create a spec through `implement`.

---

## 2. Purpose

`implement` should:

- execute tasks already defined
- stay inside the declared target areas
- update the artifacts required by the confirmed impacts
- record progress and validation evidence

It should not redesign the spec or invent new requirements.

---

## 3. Required Inputs

Always load:

```text
specs/NNN-change-name/spec.md
specs/NNN-change-name/plan.md
specs/NNN-change-name/tasks.md
```

Load these when present and relevant:

```text
specs/NNN-change-name/checklists/
specs/NNN-change-name/contracts/
specs/NNN-change-name/data-model.md
specs/NNN-change-name/review.md
.specify/memory/app/capabilities/<name>.md
.specify/memory/app/*.md referenced by Change Impact
existing source, test, and documentation files inside Target Areas
```

If planning, tasking, or review readiness is clearly missing, stop and recommend the earlier command rather than implementing through ambiguity.

### Staleness Gate

Before executing, check freshness. If `spec.md` is newer than `plan.md` or `tasks.md` (mtime), do not implement against stale plans:

- stop
- report which artifacts are stale relative to the spec
- recommend `plan` or `tasks` to refresh before implementing

---

## 4. Universal Rules

- Execute only what is defined by the current spec, plan, and tasks.
- Do not change `spec.md` intent unless explicitly instructed.
- Do not invent behavior outside the confirmed impacts.
- Keep changes within target areas.
- Record progress in `tasks.md`.
- Update validation evidence when validation is performed.
- When `code` or `tests` impacts exist, run the project's build and test commands. Non-zero exit codes are blockers.
- When `code` or `data-model` impacts exist, leave the project runnable: `start`/`dev`, `build`, `test`, and (for `data-model`) `db:create`/`db:migrate`/`db:seed` scripts must exist and exit 0. DB connection config (`.env`, `config/`) must be wired so scripts can actually connect.
- When `user-documentation` impact exists, produce `docs/users/<feature>.md`. When `system-documentation` impact exists, produce `docs/system/<feature>.md`. If `data-model` is in impact, system docs must include an ERD (Mermaid). See `IMPACT_ANALYSIS.md` for the full diagram-trigger table.
- When producing or modifying `docs/users/<feature>.md` or `docs/system/<feature>.md`, also update the `## Documentation` section of `.specify/memory/app/capabilities/<feature>.md` so the `last updated by NNN-spec-name` back-pointer matches the active spec. For `(modify)` verbs, **merge** new content into the existing doc file — do not overwrite it. See `IMPACT_ANALYSIS.md` Documentation Ownership for the full rule.

---

## 5. What `implement` Must Do

Execute the concrete artifact work required by the confirmed impacts.

Typical targets include:

- capability files
- project memory files
- contracts
- code
- tests
- documentation

When a spec declares broader impacts such as:

```text
capability
architecture
design-rule
policy
glossary
business-requirement
technology-stack
memory
constitution
```

do not treat them as optional side notes. Update or explicitly propose the corresponding artifacts.

---

## 6. Progress Recording

Update `tasks.md` with:

- completed tasks
- changed files
- tests run
- validation performed
- remaining gaps
- follow-up work

When validation is performed, create or update:

```text
specs/NNN-change-name/validation-report.md
```

when that artifact materially helps the record.

---

## 7. Completion Standard

Implementation is complete only when:

- required tasks are complete
- declared impacts were actually addressed
- changed files stayed within target areas
- tests, docs, contracts, and project-memory updates were made when required
- validation evidence is visible when validation was performed

Do not infer completion just because code changed.

---

## 8. Must Not Do

Do not:

- change spec intent silently
- redesign architecture through implementation
- create new sibling specs
- ignore declared app-memory or capability work
- modify unrelated repository areas

---

## 9. Final Rule

`implement` executes decisions already made by the current spec, plan, and tasks.

It should update the artifacts required by the confirmed impacts and nothing more.
