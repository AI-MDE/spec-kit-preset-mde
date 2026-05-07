# mde: Next

Advance the active spec using artifact state and its `Change Impact` list.

The user-facing workflow stays simple:

```text
specify -> next
```

`specify` creates a new spec. `clarify` refines it. `next` chains advancement steps until the spec reaches `COMPLETE`, hits a blocker, or surfaces something that genuinely needs the user. One `next` invocation can run through clarify → plan → tasks → analyze → promote → implement in a single go.

---

## 1. Purpose

`next` is the workflow sequencer for one active spec. It runs steps in a loop until one of the stop conditions in section 4 fires.

Each iteration of the loop decides the next step from:

- the active spec folder
- the current artifact state
- unresolved questions
- the spec's `## Change Impact`
- the spec's `## Target Areas`

`next` should not invent a parallel lifecycle model. It reads the artifacts that already exist, runs the appropriate underlying command, then re-evaluates and continues. Underlying commands own detailed artifact rules; `next` owns sequencing and stop decisions.

---

## 2. Active Spec Resolution

`next` operates on one active spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, run `status` or ask the user. If no spec exists, route to `specify`.

---

## 3. Shared Status Model

`next` and `status` must use the same artifact-based status model.

Infer status from visible artifacts, not from a separate status file.

Core statuses:

```text
DRAFT
READY WITH QUESTIONS
READY FOR PLAN
PLANNED
TASKED
NEEDS REVIEW
IMPLEMENTATION READY
IN PROGRESS
BLOCKED
COMPLETE
```

Use conservative inference. Do not claim completion unless the artifacts clearly support it.

---

## 4. Auto-Advance Loop

`next` runs the following loop:

```text
loop:
  evaluate active spec status
  if a stop condition fires -> stop and report
  otherwise pick the next step from the canonical order and run it
  re-evaluate
```

### Stop conditions

Halt the loop and return when any of these are true:

1. **`COMPLETE`** — implementation and validation evidence support completion.
2. **`BLOCKED`** — `analyze` reports blockers, or any underlying command returns a blocker.
3. **Fresh question file** — a `questions.md` was *created or materially extended in this invocation* with new `proposed` entries. The user must see and approve them; do not auto-approve questions that the user has not yet had a chance to review.
4. **Pre-existing unresolved questions in chat-mode** — `questions.md` exists with `proposed` entries from a *prior* invocation and the user has not said `next` since. (When the user invokes `next`, those count as approved per `QUESTION_POLICY.md`; the loop continues.)
5. **Underlying command error** — file system or runtime error from a step.
6. **Build or test failure** — `implement` ran the project's build or test commands and one returned a non-zero exit code. Halt with `BLOCKED` and report the failing command.
7. **Loop guard** — same step would be selected twice in a row without progress (prevents infinite loops on stuck states).

Promotion of `proposed-updates.md` and execution of `implement` are part of the loop; they do *not* stop it. `next` carries the user's authority through the chain per the question/promotion policy. If you want a single-step cadence, use the underlying primitive (`clarify`, `plan`, `tasks`, etc.) directly.

### Canonical advancement order

```text
questions -> clarify -> plan -> tasks -> analyze -> promote -> implement/finalize
```

`checklist` is on-demand validation, not part of the auto-advance chain. Invoke it explicitly when readiness signals are needed.

This order is impact-aware, not stage-aware.

### Universal rules

- Be short and concrete.
- Use existing artifacts as evidence.
- Respect `preset/QUESTION_POLICY.md`.
- Do not mutate a different spec than the active one.
- Do not create a new sibling spec through `next`.
- Use `questions.md` when multiple questions exist.
- Use `Change Impact` and `Target Areas` as the main planning surface.

---

## 5. Required Signals

Minimum signals for the active spec:

```text
spec.md
questions.md
checklists/
plan.md
tasks.md
review.md
validation-report.md
```

Inside `spec.md`, pay special attention to:

```md
## Change Impact
## Target Areas
## Out of Scope
```

If `Change Impact` or `Target Areas` is missing, incomplete, contradictory, or clearly stale, the spec is not ready for reliable planning.

---

## 6. Question Handling

Follow `preset/QUESTION_POLICY.md`.

If `questions.md` exists with proposed answers and the user says `next`:

- treat the proposed answers as approved unless the user explicitly says otherwise
- apply the approved answers to the active spec artifacts
- mark the question statuses accordingly

If multiple unresolved questions remain:

- create or update `questions.md`
- stop for review

Do not ask a long sequence of one-off chat questions when a question file is the better artifact.

---

## 7. Impact-Driven Advancement

Each loop iteration applies the steps below. After running a step, the loop re-evaluates and either advances to the next step, runs the same step again if state still calls for it, or stops per the stop conditions in section 4.

### Step 1: Clarify impact if needed

If any of these are true:

- `spec.md` is missing required sections
- `Change Impact` is missing
- `Target Areas` is missing
- impact lines lack target paths
- the change seems broader than the declared impacts
- capability links are vague or unresolved
- target areas do not cover the impacts

then the next action is:

```text
clarify
```

Goal:

- make the spec explicit enough to plan
- repair `Change Impact`
- repair `Target Areas`
- resolve capability references

### Step 2: Confirm impact for planning

If the spec is explicit enough but `plan.md` does not exist yet, or the current plan is stale relative to the spec:

the next action is:

```text
plan
```

Planning should:

- confirm or refine `Change Impact`
- translate the impacts into workstreams
- surface promotions into project memory
- identify impacted contracts, docs, tests, and code areas

### Step 3: Create tasks from confirmed impacts

If `plan.md` exists but `tasks.md` does not, or if tasks no longer match the current impacts:

the next action is:

```text
tasks
```

Tasks should:

- cover every confirmed impact area
- cover every required target area
- include app-memory work when promotions are part of the spec
- include capability-file work when capability impacts exist
- include code, tests, contracts, and documentation work when those impacts exist

Do not require exactly one task per impact. Require that every impact is covered.

### Step 4: Review readiness

If tasks exist but readiness is still unclear:

the next action is:

```text
analyze
```

Analysis should check whether:

- declared impacts are actually covered
- target areas and tasks agree
- capability links resolve
- promotions to project memory are visible
- blockers remain

If blockers are visible, stop and report them.

### Step 5: Promote pending app-memory updates

Before treating any spec as `IMPLEMENTATION READY`, drain `.specify/memory/app/proposed-updates.md`.

For each pending entry:

- present the proposed change to the user (target file, content, source citation)
- on approval, write the change to the target memory file (e.g. `policies.md`, `design-rules.md`, `architecture.md`, `capabilities/<name>.md`)
- mark the entry as `promoted` in `proposed-updates.md` (or remove it)
- on rejection, mark it `rejected` with a one-line reason
- on defer, leave it as `proposed` and note the deferral

If the active spec declared `propose` impacts, those proposals must land here before implementation. Do not let approved knowledge stay buried in `proposed-updates.md`.

### Step 6: Implement or finalize

If planning, tasks, and readiness evidence are in place and blockers are not visible:

the next action is:

```text
implement
```

Implementation should update the concrete artifacts required by the impacts:

- capability files
- project memory
- contracts
- code
- tests
- documentation

Do not treat implementation as a substitute for missing clarification or missing impact analysis.

---

## 8. Staleness Rules

`next` must notice when downstream artifacts are stale relative to the current spec.

Treat `plan.md`, `tasks.md`, or `review.md` as stale when the spec materially changed in:

- behavior
- `Change Impact`
- `Target Areas`
- capability links
- contracts
- acceptance criteria

When stale artifacts are detected:

- do not pretend the spec is farther along than it is
- route back to the earliest downstream artifact that needs refresh

Examples:

- changed `Change Impact`, no updated plan -> go to `plan`
- plan updated, tasks still match old impacts -> go to `tasks`
- tasks updated, readiness still unclear -> go to `analyze`

---

## 9. Project Memory and Capability Checks

`next` should treat broader impacts as real work, not as side notes.

When the spec declares impacts such as:

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

make sure the workflow includes the corresponding artifact work.

Do not let a spec reach `IMPLEMENTATION READY` if app-memory or capability work is declared but ignored.

---

## 10. Completion Rule

Treat a spec as `COMPLETE` only when local artifacts support that reading.

Completion usually means:

- the spec is clarified enough
- impacts are confirmed
- tasks cover the impacts
- blockers are resolved or explicitly accepted
- implementation or finalization artifacts support the claimed outcome

Do not infer completion just because some files exist.

---

## 11. Response Format

Always report:

```text
Active spec: <folder or none>
Steps run: <ordered list of steps executed this invocation, e.g. clarify -> plan -> tasks -> analyze>
Final status: <DRAFT | READY WITH QUESTIONS | READY FOR PLAN | PLANNED | TASKED | NEEDS REVIEW | IMPLEMENTATION READY | IN PROGRESS | BLOCKED | COMPLETE>
Stop reason: <COMPLETE | BLOCKED | fresh-questions | error | loop-guard>
Impact summary: <short summary of affected areas>
Files changed: <list or none>
Next: <what happens when user says next, or "none — spec is complete" / "fix blockers and re-run">
```

When stopping for blockers or fresh questions, name the file the user must look at and what to do.

---

## 12. Final Rule

`next` should move the active spec forward by reading the current artifacts and the current impact list.

If the impacts are unclear, fix the spec first.
If the impacts are clear, use them to drive planning, tasks, review, and implementation.
