# mde: Plan

Create or update `plan.md` for the active spec.

`plan` is an impact-aware adapter. It turns a clarified spec into an execution approach.

---

## 1. Active Spec Resolution

`plan` operates on one existing spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, ask. If none exists, route to `specify`. Never create a spec through `plan`.

---

## 2. Purpose

`plan` should:

- confirm or refine `Change Impact`
- translate impacts into workstreams
- identify the artifacts expected to change
- surface required promotions into project memory
- define how readiness and validation will be checked

`plan` should not implement code or invent new scope.

---

## 3. Required Inputs

Always load:

```text
specs/NNN-change-name/spec.md
```

Load these when present and relevant:

```text
specs/NNN-change-name/questions.md
specs/NNN-change-name/contracts/
specs/NNN-change-name/data-model.md
.specify/memory/constitution.md
.specify/memory/app/capabilities/<name>.md
.specify/memory/app/*.md referenced by Change Impact
existing source, test, and documentation files inside Target Areas
```

---

## 4. Precondition Rule

Do not proceed to real planning if `spec.md` is still missing key scope controls.

If any of these are missing or clearly stale:

```md
## Change Impact
## Target Areas
## Out of Scope
```

or if capability links are unresolved, then stop and recommend:

```text
clarify
```

`plan` may repair minor wording inconsistencies, but it must not silently do major spec refinement.

---

## 5. Planning Rules

Plan from the current spec's:

- `Change Impact`
- `Target Areas`
- requirements, rules, and acceptance criteria
- open questions and known constraints

Do not invent a second scope model in `plan.md`.

The plan should be impact-aware, not phase-aware.

---

## 6. What `plan` Must Do

### Confirm Impact

Review the current `Change Impact` list and make sure it is usable for downstream work.

Validate that:

- every impact has a target path
- broader impacts are not hidden inside local code-only wording
- target areas cover the declared impacts
- capability impacts resolve to durable capability files

### Build Workstreams

Group the plan by repository concerns that follow from the impacts.

Typical workstreams:

- capability update
- project-memory promotion
- contracts
- code changes
- tests
- documentation
- validation

### Define Artifact Strategy

State which artifacts are expected to be created or updated.

Typical examples:

```text
.specify/memory/app/capabilities/<name>.md
.specify/memory/app/design-rules.md
contracts/
src/
tests/
docs/
review.md
validation-report.md
```

### Define Validation Strategy

State how success will be checked.

Examples:

```text
checklists
tests
review.md
validation-report.md
```

### Surface Promotions

If the spec implies broader application truth, make that visible in the plan.

Examples:

- architecture rule promotion
- glossary update
- technology-stack update
- policy update
- capability catalog update

Do not leave these as buried side effects.

---

## 7. `plan.md` Shape

Create or update `plan.md` using a shape like:

```md
# Plan - <Spec Name>

## Planning Goal

## Inputs Reviewed

## Impact Summary

## Workstreams

## Artifact Strategy

## Validation Strategy

## Risks and Open Questions
```

---

## 8. Allowed Side Effects

Primary target:

```text
specs/NNN-change-name/plan.md
```

Optional secondary target when planning reveals missing clarifications:

```text
specs/NNN-change-name/questions.md
```

Do not write code, tests, or implementation artifacts during planning.

Do not create trace-map, implementation-contract, or JSON companion files by default.

---

## 9. Final Rule

`plan` should make the current spec executable without changing its intent.

If impacts are unclear, stop for `clarify`.
If impacts are clear, turn them into workstreams and artifact expectations.
