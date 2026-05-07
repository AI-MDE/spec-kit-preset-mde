# mde: Checklist

Create or update checklists for the active spec.

`checklist` is an impact-aware adapter. It validates spec readiness and downstream artifact quality against the current impacts and target areas.

---

## 1. Active Spec Resolution

`checklist` operates on one existing spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, ask. If none exists, route to `specify`. Never create a spec through `checklist`.

---

## 2. Purpose

`checklist` should validate:

- spec readiness
- planning readiness
- task coverage
- implementation or validation readiness

It should not invent requirements or redesign the spec.

---

## 3. Universal Rules

- Do not change `spec.md`, `plan.md`, `tasks.md`, or implementation files unless explicitly asked.
- Create or update checklist files under `checklists/`.
- Use the current spec artifacts as the source of truth.
- Validate against `Change Impact` and `Target Areas`.
- Use the smallest checklist set that gives clear readiness signals.

---

## 4. Required Inputs

Always load:

```text
specs/NNN-change-name/spec.md
specs/NNN-change-name/checklists/
```

Load these when present and relevant:

```text
specs/NNN-change-name/plan.md
specs/NNN-change-name/tasks.md
specs/NNN-change-name/contracts/
specs/NNN-change-name/data-model.md
specs/NNN-change-name/review.md
.specify/memory/app/capabilities/<name>.md
.specify/memory/app/*.md referenced by Change Impact
```

---

## 5. Staleness Gate

Before producing checklists, check freshness. If `spec.md` is newer than `plan.md` or `tasks.md` (mtime), the artifacts the checklist would validate are stale. In that case:

- produce a `spec-readiness.md` checklist only
- do not produce planning, task, or implementation checklists against stale artifacts
- recommend `plan` or `tasks` to refresh first

---

## 6. Checklist Types

Choose checklist content from current artifact state, not from a phase model.

### Spec Readiness

Use when the spec is still being clarified or is about to enter planning.

Validate:

- `spec.md` exists
- `Change Impact` exists
- every impact has a target path
- `Target Areas` exists
- target areas cover the impacts
- `Out of Scope` exists
- requirements or rules are explicit enough to act on
- capability links are explicit when capability impact exists
- open questions are visible

### Planning Readiness

Use when `plan.md` exists or is about to be created.

Validate:

- `plan.md` reflects the current spec
- impacts are confirmed or explicitly refined in planning
- every meaningful impact area has a corresponding workstream
- project-memory promotions are visible when broader impacts exist
- required contracts, code, tests, and documentation areas are identified

### Task Coverage

Use when `tasks.md` exists or is about to be created.

Validate:

- every confirmed impact area is covered by tasks
- tasks stay within target areas
- capability-file work is included when capability impact exists
- app-memory work is included when broader promotion is required
- validation work is included

### Implementation / Validation Readiness

Use when work appears ready to implement or validate.

Validate:

- changed files are expected to stay within target areas
- declared impacts are actually addressed
- required contracts, tests, and documentation are included
- project-memory updates are completed or explicitly proposed
- `review.md` and `validation-report.md` are present when required

---

## 7. File Placement

Create or update checklist files under:

```text
specs/NNN-change-name/checklists/
```

Typical names:

```text
spec-readiness.md
planning-readiness.md
task-coverage.md
implementation-readiness.md
```

Use only the files that materially help the current spec.

---

## 8. Output Style

When reporting what `checklist` is doing:

- name the target spec
- name the checklist file or files updated
- state whether the main issues are missing impacts, missing task coverage, or readiness blockers

---

## 9. Final Rule

`checklist` validates the current spec and its downstream artifacts against the declared impacts.

It should expose readiness gaps, not invent a phase boundary.
