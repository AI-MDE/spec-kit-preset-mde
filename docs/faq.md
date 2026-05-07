# FAQ

Short answers to the questions users hit first when using the mde preset.

## How do I redo a spec?

First decide what "redo" means.

### 1. I want to refine the current spec

Use:

```text
clarify <what needs to change>
```

Example:

```text
clarify add an audit trail for onboarding approvals
```

Use `clarify` when the scoped change is still the same change and you want to update:

- behavior
- requirements
- `Change Impact`
- `Target Areas`
- capability links

Do not create a new spec just to refine the current one.

### 2. I want to continue work on an older spec

Switch to the branch for that spec, then use:

```text
clarify <what needs to change>
```

If you are unsure which spec is active, run:

```text
status
```

If you need to scan broader repository state, run:

```text
status project
```

### 3. I want a separate follow-up change against the same capability

Create a new spec:

```text
specify <new change>
```

Use `specify` when the work is a new scoped change, even if it touches a capability that already exists.

Example:

```text
specify add audit trail to employee onboarding
```

That new spec should usually modify the existing capability file in `.specify/memory/app/capabilities/` rather than create a new capability.

### 4. I want to throw away a bad draft and start over

Do that intentionally, not implicitly.

Recommended approach:

1. Confirm the current branch and active spec.
2. Decide whether the current spec should be archived or deleted.
3. Remove the abandoned draft artifacts yourself.
4. Run `specify` again with the corrected request.

mde should not silently overwrite one spec with a brand-new one.

## How do I regenerate a spec after I changed app rules?

You changed a design rule, policy, technology stack entry, or constitution principle, and you want an existing spec to honor the new rule.

Two paths.

### Path 1 — refine in place (most cases)

Use when the spec's intent is still valid and only *how* to do it changed.

```text
1. Make sure the new rule is in memory or constitution
   (edit .specify/memory/app/<file>.md, or via specify -> next -> promote)

2. git switch <spec-branch>

3. clarify <hint about what changed>
   e.g. clarify in light of the new audit policy

4. next
```

What happens:

- `clarify` re-reads project memory (the new rules included), updates `## Change Impact` and `## Target Areas`, may add Open Questions
- `spec.md` becomes newer than `plan.md` / `tasks.md` / `review.md`
- `next`'s staleness gate routes back through `plan` → `tasks` → `analyze` → `implement`, regenerating each
- Build and test re-run as part of the chain

The spec folder is the same; the regeneration is preserved in `tasks.md` completion notes and the session log.

### Path 2 — supersede with a new spec

Use when the rule change invalidated the spec's *intent*, not just its execution.

```text
1. Mark the old spec superseded:
   in review.md: Status: SUPERSEDED-BY specs/NNN-new-name

2. specify <new direction>
   creates a new spec targeting the same capability

3. next
```

Multiple specs targeting the same capability is normal — that's why capability and spec are separate.

### Decision rule

| Question | Path |
|---|---|
| Spec's `## Summary` and intent still describe what you want? | refine |
| Rule change shifted *what* you're building, not just *how*? | supersede |
| New impacts still belong together as one scoped change? | refine |
| New impacts split cleanly into separate changes? | supersede |

### Watch out

The new rule must actually be in memory before you `clarify`. If you only changed it in chat, `clarify`'s read-before-decide will read the *old* rule from `.specify/memory/app/` and produce the same output as before.

If the rule change is broader than the spec, do the rule change as its own scoped change first (so it's audit-trailed and promoted properly), then refine the affected spec.

---

## When do I use `specify` vs `clarify`?

Use:

- `specify` to create a new spec
- `clarify` to refine the active spec

Simple rule:

- same scoped change -> `clarify`
- new scoped change -> `specify`

## Can multiple specs affect the same capability?

Yes.

That is normal.

In this preset:

- `capability` is the durable system behavior
- `spec` is one scoped change against that behavior

So a capability may be created, extended, constrained, fixed, or reworked by multiple specs over time.

## Why not use `feature` for the durable concept?

Because Spec Kit upstream uses `feature` in a spec-sized, work-item sense.

This preset uses:

- `feature` only when referring to upstream Spec Kit wording
- `capability` for the long-lived system concept

## What does `status` do?

`status` is read-only.

Modes:

- `status` -> report on the current spec only
- `status project` -> scan for stale or incomplete specs
- `status capabilities` -> scan for incomplete or stale capabilities

## How do I modify the constitution with the revised preset template?

Use:

```text
constitution <project-wide rule change>
```

Examples:

```text
constitution adopt the revised mde constitution template and preserve any project-specific rules that do not conflict
```

```text
constitution revise the constitution to the current mde model:
- no phase model
- specify creates new specs only
- clarify refines the active spec
- capabilities are durable system behaviors
- every spec must declare Change Impact, Target Areas, and Out of Scope
```

Use `constitution` when the change is project-wide and belongs in:

```text
.specify/memory/constitution.md
```

Use `clarify` instead when the change belongs only to the active spec.

Simple rule:

- project-wide rule -> `constitution`
- spec-local refinement -> `clarify`

## Where does durable truth live?

In project memory:

```text
.specify/memory/app/
```

Specs are change records. Memory is repository truth.

## What if `next` stops?

That is expected.

`next` should stop at meaningful boundaries such as:

- new questions
- blockers
- stale downstream artifacts
- readiness for implementation review
- completion

Use `status` to see what is active and what should happen next.
