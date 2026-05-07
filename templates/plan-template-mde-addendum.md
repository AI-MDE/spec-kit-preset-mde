# mde Plan Addendum

Use this addendum when creating or reviewing `plan.md`.

The plan should translate the spec's confirmed impacts into an execution approach.

---

## Planning Rule

Plan from the spec's current:

- `## Change Impact`
- `## Target Areas`
- requirements, rules, and acceptance criteria
- open questions and known constraints

Do not invent a second scope model in `plan.md`.

---

## Minimal Scaffold

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

## What Each Section Should Do

### `## Planning Goal`

State what this plan is trying to achieve for the current spec.

### `## Inputs Reviewed`

Record the artifacts actually used to plan the work.

Examples:

```text
spec.md
.specify/memory/app/capabilities/employee-onboarding.md
.specify/memory/app/design-rules.md
existing contracts/
relevant source files
```

### `## Impact Summary`

Confirm or refine the impacts from `spec.md`.

Summarize:

- what is changing
- which repository areas are affected
- which impacts are local versus promoted into project memory

### `## Workstreams`

Group the work by impact area rather than by artificial phase.

Examples:

- capability update
- app-memory promotion
- contracts
- code changes
- tests
- documentation

### `## Artifact Strategy`

State which artifacts are expected to be created or updated.

Examples:

```text
capability file
project memory files
contracts/
code
tests
documentation
```

### `## Validation Strategy`

State how success will be checked.

Examples:

```text
checklists
tests
review.md
validation-report.md
```

### `## Risks and Open Questions`

Record uncertainty, blockers, or unresolved edge cases instead of hiding them.

---

## Planning Invariants

The plan should:

- confirm that `Change Impact` is usable
- cover every meaningful impact area
- preserve the spec's `Target Areas`
- surface required project-memory updates
- make capability updates explicit when capability impacts exist

The plan should not:

- create a trace-map by default
- create a separate implementation-contract file by default
- invent new work outside the spec's scope
- treat implementation as planning
