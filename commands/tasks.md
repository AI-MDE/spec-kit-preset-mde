# mde: Tasks

Create or update `tasks.md` for the active spec.

`tasks` is an impact-aware adapter. It turns confirmed impacts into actionable work.

---

## 1. Active Spec Resolution

`tasks` operates on one existing spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, ask. If none exists, route to `specify`. Never create a spec through `tasks`.

---

## 2. Purpose

`tasks` should:

- derive work from confirmed impacts
- cover the declared target areas
- define validation work
- make implementation scope concrete

`tasks` should not invent new scope or change spec intent.

---

## 3. Required Inputs

Always load:

```text
specs/NNN-change-name/spec.md
specs/NNN-change-name/plan.md
```

Load these when present and relevant:

```text
specs/NNN-change-name/questions.md
specs/NNN-change-name/contracts/
specs/NNN-change-name/data-model.md
.specify/memory/app/capabilities/<name>.md
.specify/memory/app/*.md referenced by Change Impact
```

If `plan.md` does not exist or is clearly stale relative to the spec, stop and recommend:

```text
plan
```

---

## 4. Tasking Rule

Tasks should be derived from:

- confirmed `Change Impact`
- `Target Areas`
- the current `plan.md`
- acceptance criteria
- unresolved risks that still require action

Every confirmed impact should be covered by tasks.

Do not require exactly one task per impact. Require that every impact is covered.

---

## 5. What `tasks` Must Do

### Cover Impact Areas

Tasks should cover all meaningful impact areas, including when relevant:

- capability files
- project memory
- contracts
- code
- tests
- documentation
- validation

If the spec includes app-memory or capability impacts, do not omit that work from `tasks.md`.

### Respect Target Areas

Tasks must stay within the declared target areas.

If target areas are too vague or too broad to produce safe tasks, stop and recommend:

```text
clarify
```

### Define Validation Work

Include tasks for:

- tests
- run the project's build command and test command; record exit codes
- when `code` or `data-model` impacts exist, wire up runnable scripts (`start`, `db:create`, `db:migrate`, `db:seed`) and DB connection config, and verify each by running it
- when `user-documentation` impact exists, write `docs/users/<feature>.md` with screenshots or screen-flow diagram
- when `system-documentation` impact exists, write `docs/system/<feature>.md`; if `data-model` is in impact, include an ERD (Mermaid); add other diagrams (component, sequence, state) when their trigger impacts apply
- checklist or readiness review
- review.md updates when needed
- validation-report.md when validation is performed

### Record Controlled Context

For non-trivial implementation, include:

```md
## AI Implementation Context

### Read
- <relevant files>

### May Modify
- <allowed write paths>

### Do Not Touch
- <out-of-scope paths>
```

This context should match `Target Areas`.

---

## 6. `tasks.md` Shape

Create or update `tasks.md` using a shape like:

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

## 7. Completion Notes Rule

Use the bottom of `tasks.md` to capture:

- changed files
- tests run
- remaining gaps
- follow-up work

This is where implementation progress should accumulate. Do not create a separate implementation report by default.

---

## 8. Allowed Side Effects

Primary target:

```text
specs/NNN-change-name/tasks.md
```

Do not create trace-map, implementation-contract, or JSON companion files by default.

Do not write implementation code during task generation.

---

## 9. Final Rule

`tasks` should make the work concrete enough to execute.

If planning is not stable, route back to `plan`.
If impacts are confirmed, ensure every impact is covered by actionable work.
