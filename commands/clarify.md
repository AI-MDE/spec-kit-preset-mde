# mde: Clarify

Refine the current spec in place.

`clarify` is a first-class refinement command. It updates an existing spec. It does not create new work.

User-facing intent:

```text
specify -> clarify -> next
```

Terminology note:

```text
Spec Kit "feature" -> spec / scoped work item
mde capability  -> durable system behavior
```

If the user wants new work, use `specify` instead.

---

## 1. Core Rule

`clarify` means:

```text
refine the current spec
```

Use it when the user wants to:

- revise scope
- answer open questions
- refine behavior
- tighten acceptance criteria
- correct or expand `Change Impact`
- adjust `Target Areas`
- resolve capability references

Do not use `clarify` to:

- create a new spec folder
- switch branches
- silently split work into a new sibling spec
- implement code as a substitute for refining the spec

---

## 2. Active Spec Resolution

`clarify` operates on one existing spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, list candidates and ask. If none exists, route to `specify`. Never create a spec through `clarify`.

---

## 3. Primary Update Targets

Primary target:

```text
specs/NNN-change-name/spec.md
```

Secondary targets when needed:

```text
specs/NNN-change-name/questions.md
specs/NNN-change-name/review.md
```

`clarify` may also update existing spec-owned design artifacts when the clarification directly changes them and they already exist:

```text
specs/NNN-change-name/data-model.md
specs/NNN-change-name/contracts/
```

Do not create or rewrite downstream execution artifacts unless the clarification directly requires it.

In particular, do not silently regenerate:

```text
tasks.md
validation-report.md
```

If clarification invalidates downstream artifacts, record that clearly and let `next`, `plan`, `tasks`, or `analyze` refresh them.

---

## 4. Required Spec Sections

When refining `spec.md`, preserve or repair these sections:

```md
## Change Impact

## Target Areas

## Out of Scope
```

If any are missing, add them.

`clarify` should leave the spec more explicit than it found it.

---

## 5. Change Impact Refinement

`clarify` owns refinement of `Change Impact`.

Use it to:

- add missing impact areas
- remove impacts that no longer belong
- correct target paths
- promote the change to a broader level when needed

Impact lines should use:

```text
<impact-area>: <target path> (<verb>)
```

Supported verbs:

```text
create
modify
remove
propose
```

Apply the highest valid change rule:

```text
constitution
application
capability
spec only
```

Do not hide a broader architecture, policy, glossary, data-model, or technology change inside local code-only wording.

---

## 6. Target Areas Refinement

`clarify` owns refinement of `Target Areas`.

Use it to ensure the spec states where change is allowed.

Validate that:

- target areas cover the declared impacts
- target areas are concrete paths or clear path patterns
- the target set is neither too narrow to implement nor so broad that scope disappears

Examples:

```text
src/onboarding/
tests/onboarding/
docs/upgrade-notes.md
specs/NNN-change-name/contracts/
.specify/memory/app/capabilities/employee-onboarding.md
.specify/memory/app/design-rules.md
```

---

## 7. Capability Resolution

When the spec affects a capability, `clarify` should make that explicit and durable.

Validate that:

- capability impacts point to `.specify/memory/app/capabilities/<name>.md`
- capability names are consistent across the spec
- a `modify` impact points to an existing capability file, or the gap is called out
- a `create` impact makes clear that the capability file is expected to be created

If the capability reference is vague or overloaded, refine it.

Bad:

```text
- capability: onboarding (modify)
```

Better:

```text
- capability: .specify/memory/app/capabilities/employee-onboarding.md (modify)
```

---

## 8. Questions Rule

If refinement reveals multiple unresolved questions, create or update:

```text
specs/NNN-change-name/questions.md
```

Each question should include:

```text
question
default answer
rationale
impact if changed
status
```

Do not ask a long series of one-off chat questions when a question file is the better artifact.

---

## 9. Downstream Invalidation Rule

Clarification can make downstream artifacts stale.

If the refined spec materially changes:

- behavior
- change impact
- target areas
- capability links
- contracts
- acceptance criteria

then say which downstream artifacts likely need refresh, for example:

```text
plan.md should be reviewed
tasks.md likely needs regeneration
review.md may no longer reflect the current scope
```

Do not pretend existing downstream artifacts are still valid when the spec changed materially.

---

## 10. Response Style

Be short and concrete.

When reporting what `clarify` is doing:

- name the target spec
- state what was refined
- state whether open questions remain
- state whether downstream artifacts may need refresh

Good example:

```text
Clarifying `specs/120-change-onboarding-validation/spec.md`.
Updated `Change Impact`, tightened `Target Areas`, and resolved the capability link to `employee-onboarding.md`.
Open questions were recorded in `questions.md`.
`plan.md` should be reviewed before continuing.
```

Bad example:

```text
The clarify behavior depends on which preset phase override is active.
```

---

## 11. Final Rule

`clarify` makes an existing spec more explicit, more bounded, and more internally consistent.

It refines work already in progress. It does not create new work.
