# mde: Analyze

Audit the active spec artifacts for coverage, consistency, and readiness.

`analyze` is an impact-aware adapter. It checks whether the declared impacts are actually covered and whether the current artifact set is coherent enough to proceed.

---

## 1. Active Spec Resolution

`analyze` operates on one existing spec. Resolve the target using [`../RESOLUTION.md`](../RESOLUTION.md): explicit folder > branch match > single in-flight spec. If ambiguous, ask. If none exists, route to `specify`. Never create a spec through `analyze`.

---

## 2. Purpose

`analyze` should:

- identify gaps, inconsistencies, and blockers
- test whether impacts are actually covered
- check capability resolution and target-area alignment
- decide whether the spec is ready for implementation or needs another pass

It should not create new requirements or redesign the spec.

---

## 3. Required Inputs

Always load:

```text
specs/NNN-change-name/spec.md
```

Load these when present and relevant:

```text
specs/NNN-change-name/plan.md
specs/NNN-change-name/tasks.md
specs/NNN-change-name/checklists/
specs/NNN-change-name/contracts/
specs/NNN-change-name/data-model.md
specs/NNN-change-name/review.md
specs/NNN-change-name/validation-report.md
.specify/memory/app/capabilities/<name>.md
.specify/memory/app/*.md referenced by Change Impact
existing source, test, and documentation files inside Target Areas
```

---

## 4. Core Checks

### Staleness Gate

Before checking content, check freshness. If `spec.md` is newer than `plan.md` or `tasks.md` (mtime), the downstream artifacts are stale. In that case:

- do not certify the spec as ready
- report which artifacts are stale relative to the spec
- recommend `plan` or `tasks` (whichever is older than `spec.md`)

### Scope Integrity

Check that:

- `Change Impact` exists
- every impact has a target path
- `Target Areas` exists
- target areas cover the impacts
- `Out of Scope` exists

If these are not true, the spec is not ready. Route back to:

```text
clarify
```

### Impact Coverage

Check that:

- `plan.md` covers the meaningful impacts
- `tasks.md` covers the confirmed impacts
- broader promotions are visible when app-memory changes are implied
- declared documentation, contract, test, and code impacts are not silently dropped

### Documentation Impact Coverage

For each `capability:` impact in `Change Impact`, verify the derived documentation impacts are present or explicitly justified, per the **Derived Documentation Impacts** rule in `IMPACT_ANALYSIS.md`:

- `capability: <feature> (modify)` requires `system-documentation: docs/system/<feature>.md (modify)` and `user-documentation: docs/users/<feature>.md (modify)`, OR a one-line justification in the spec for each omission.
- `capability: <feature> (create)` requires `system-documentation: ...(create)`; `user-documentation: ...(create)` is required when the capability has user-visible behavior.
- `capability: <feature> (remove)` requires the matching `(remove)` doc impacts.

A missing derived doc impact without a justification is a `NOT READY` blocker.

Also verify the **back-pointer** in `.specify/memory/app/capabilities/<feature>.md`'s `## Documentation` section: when a doc impact claims `(modify)`, the spec name must appear or be scheduled to appear in the back-pointer's `last updated by` line after `implement`. If the back-pointer is absent from the capability file entirely, flag it as a consistency gap.

### Orphan Documentation

Cross-check the file system against the capability catalog:

- For each `docs/system/*.md` and `docs/users/*.md` file present in the repo, check that a live capability file exists at `.specify/memory/app/capabilities/<feature>.md`.
- If no live capability matches, the doc is **orphaned** (capability renamed, removed, or never registered). Surface as a warning in `## Risks`, not a blocker — the active spec may not own the cleanup.
- Do not delete orphan docs from `analyze`. Recommend a cleanup spec.

### Capability Resolution

When capability impact exists, check that:

- capability links are explicit
- the capability file path is durable
- create versus modify intent is coherent
- related spec and capability wording do not drift

### Artifact Consistency

Check that:

- `spec.md`, `plan.md`, and `tasks.md` still describe the same work
- contracts and data notes match the declared impacts
- target areas still match the actual intended change surface
- downstream artifacts are not stale relative to recent spec changes

### Readiness

Check that:

- blockers are visible
- validation work exists
- implementation can proceed without guessing

---

## 5. Output Artifact

Create or update:

```text
specs/NNN-change-name/review.md
```

Use a shape like:

```md
# Review - <Spec Name>

## Assessment
READY | READY WITH RISKS | NOT READY

## Summary

## Impact Coverage Gaps

## Consistency Gaps

## Blockers

## Risks

## Required Follow-ups

## Recommendation
```

---

## 6. Assessment Rules

- `READY`
  No meaningful blockers. Impacts, targets, plan, and tasks are coherent enough to proceed.

- `READY WITH RISKS`
  No hard blocker, but follow-up risks or deferred edges remain visible.

- `NOT READY`
  One or more blockers prevent safe progress.

Use conservative judgment. Do not call the spec ready if impacts are still ambiguous or downstream coverage is missing.

---

## 7. tasks.md Update Rule

Only update `tasks.md` when:

- the user explicitly asks
- concrete blocker follow-ups clearly belong there

Otherwise keep the analysis in `review.md`.

---

## 8. Final Rule

`analyze` validates readiness by checking whether the active spec's declared impacts are actually covered.

It should expose gaps and blockers, not create a second design process.
