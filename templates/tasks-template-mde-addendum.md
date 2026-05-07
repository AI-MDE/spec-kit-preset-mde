# mde Tasks Addendum

Use this addendum when creating or reviewing `tasks.md`.

`tasks.md` should turn confirmed impacts into actionable work.

Do not use a separate implementation report by default.

---

## Tasking Rule

Tasks should be derived from:

- confirmed `Change Impact`
- `Target Areas`
- the current `plan.md`
- acceptance criteria
- unresolved risks that still require action

Every confirmed impact should be covered by tasks.

---

## Minimal Scaffold

```md
# Tasks - <Spec Name>

## Spec Alignment
- [ ] Review `spec.md`
- [ ] Review `plan.md`

## Impact Tasks
- [ ] <task tied to one or more impacts>

## Validation Tasks
- [ ] <tests, checklist, review, or validation work>

## Completion Notes
- changed files:
- gaps:
- follow-ups:
```

---

## Task Categories

Use only the categories that actually apply.

### Spec Alignment

Use when the implementation team needs to confirm the current artifact set first.

Examples:

```text
review spec.md
review plan.md
review current contracts
review relevant project memory
```

### Impact Tasks

Create tasks for the actual impacts.

Examples:

```text
update capability file
promote architecture rule into project memory
create or update contracts
modify code in target area
add or update tests
create or update documentation
```

### Validation Tasks

Use for readiness and outcome verification.

Examples:

```text
run tests
review impact coverage
update review.md
record validation-report.md when validation was performed
```

### Completion Notes

Use the bottom of `tasks.md` to capture:

- changed files
- tests run
- remaining gaps
- follow-up work

---

## Controlled Context

For non-trivial implementation, include an implementation-context section such as:

```md
## AI Implementation Context

### Read
- <relevant files>

### May Modify
- <allowed write paths>

### Do Not Touch
- <out-of-scope paths>
```

This section should match `Target Areas`.

---

## Tasking Invariants

Tasks should:

- cover every confirmed impact area
- include app-memory work when broader promotions are part of the spec
- include capability-file work when capability impacts exist
- include tests when behavior changes
- include documentation work when documentation impacts exist

Tasks should not:

- invent new repository areas outside the spec's target areas
- rely on phase-based scaffolds
- create trace-map or implementation-contract files by default
