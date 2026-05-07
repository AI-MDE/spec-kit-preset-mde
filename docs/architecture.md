# Preset Architecture

How the mde preset's pieces fit together. Read this after `terminology.md` if you want to extend or modify the preset.

---

## The model in one paragraph

A *capability* is a persistent system behavior, stored in `.specify/memory/app/capabilities/<name>.md`. A *spec* is a scoped change-control package in `specs/NNN-change-name/`. Specs come and go; capabilities accumulate. Each spec declares a `## Change Impact` listing which areas it touches and what verb (create / modify / remove / propose) applies. The AI reads existing project memory before assigning impacts so verbs reflect actual state, not assumed state.

---

## Layers

```text
+-------------------------------------------------------------+
|  User-facing commands  (specify, clarify, next, status)     |
+-------------------------------------------------------------+
|  Internal primitives   (plan, tasks, analyze, implement,    |
|                         checklist, constitution)            |
+-------------------------------------------------------------+
|  Shared mechanisms     (RESOLUTION.md, QUESTION_POLICY.md,  |
|                         constitution Principle IX)          |
+-------------------------------------------------------------+
|  Templates             (spec, plan, tasks, checklist,       |
|                         constitution addendums)             |
+-------------------------------------------------------------+
|  Project artifacts     (specs/, .specify/memory/, logs/)    |
+-------------------------------------------------------------+
```

User-facing commands are entry points. Primitives are invoked by `next` (or by the user directly for one-step pacing). Shared mechanisms are referenced by every primitive that needs them. Templates shape the artifacts the AI emits. Project artifacts are the durable output.

---

## Commands

### User-facing

| Command | Job | Mutation |
|---|---|---|
| `specify` | Create a new spec folder | Yes — creates `specs/NNN/` |
| `clarify` | Refine the active spec in place | Yes — edits `specs/NNN/spec.md` |
| `next` | Advance the active spec, looping until a stop condition | Yes — runs primitives in sequence |
| `status` | Read-only project state report | No |
| `setup` | Thin alias for `specify initial project setup` | Yes — first-run only |

### Internal primitives

| Primitive | Owns | Output |
|---|---|---|
| `plan` | Translating impacts into workstreams | `plan.md` |
| `tasks` | Translating plan into actionable work | `tasks.md` |
| `analyze` | Auditing readiness | `review.md` |
| `implement` | Executing tasks against target areas | code, tests, memory updates, `validation-report.md` |
| `checklist` | On-demand validation checklists | `checklists/*.md` |
| `constitution` | Project governance rules | `.specify/memory/constitution.md` |

`next` invokes primitives in this order: `clarify → plan → tasks → analyze → promote → implement`. `checklist` is on demand, not part of the chain.

---

## Shared mechanisms

### Active-spec resolution ([RESOLUTION.md](../RESOLUTION.md))

Every primitive that operates on one spec asks: *which spec?* The resolution algorithm is centralized so seven commands don't drift:

```text
1. explicit folder named in the request
2. branch numeric prefix matches a single specs/NNN-*/ folder
3. exactly one in-flight spec exists (not COMPLETE, spec.md newer than validation-report.md)
```

If ambiguous, the primitive lists candidates and asks. It never invents a spec.

### Question lifecycle ([QUESTION_POLICY.md](../QUESTION_POLICY.md))

Questions are editable artifacts, not chat loops. Lifecycle:

```text
proposed -> approved -> applied -> closed
         \-> rejected
         \-> blocked
         \-> deferred
```

When the user types `next` and `questions.md` has `proposed` entries from a prior turn, those count as approved. Fresh questions created *in this invocation* halt the loop so the user can review.

### Status inference (`commands/status.md`)

Status is derived mechanically from file presence and mtime, not from a stored status field:

```text
DRAFT             : spec.md missing key sections
READY WITH QUESTIONS : has proposed questions
READY FOR PLAN    : sections complete, questions resolved
PLANNED / TASKED  : plan.md / tasks.md present
NEEDS REVIEW      : tasks.md exists, review.md stale
IMPLEMENTATION READY : plan + tasks fresh, no blockers
IN PROGRESS       : tasks partially complete
BLOCKED           : review.md or questions report blockers
COMPLETE          : validation-report success or all tasks complete
```

Two sessions on the same artifact tree produce the same status.

### Constitution Principle IX

Session logs record every material interaction in two places:

- `logs/<YYYY-MM-DD>.md` — chronological, all entries
- `specs/NNN-change-name/session-log.md` — per-spec mirror when the entry belongs to a spec

Each entry: timestamp, actor, interaction, outcome, tokens, size. Use `NA` for unavailable values.

---

## Templates

Templates shape the artifacts the AI emits. The preset replaces five upstream Spec Kit templates with mde versions:

| Template | Role |
|---|---|
| `spec-template-mde-addendum.md` | Single spec scaffold with `## Change Impact` / `## Target Areas` / `## Out of Scope` required sections |
| `plan-template-mde-addendum.md` | Impact-driven plan structure (Workstreams, Artifact Strategy, Validation Strategy) |
| `tasks-template-mde-addendum.md` | Tasks grouped by impact area with Validation and Completion Notes |
| `checklist-template-mde-addendum.md` | Spec / Planning / Tasks / Implementation readiness checklists |
| `constitution-template-mde-addendum.md` | Nine governance principles including Session Accountability |

Templates do *not* enforce a phase model. They enforce shape (required sections, impact format) and let change-type variation play out in content.

---

## How a single spec flows through the preset

```text
user: specify add audit trail to onboarding
  |
  v
specify reads project memory, writes specs/160-add-onboarding-audit-trail/spec.md
with inferred Change Impact (capability=modify, policy=propose, code=modify, tests=add)
  |
  v
user: next
  |
  v
next.loop {
  clarify  -> spec needs no refinement, skip
  plan     -> writes plan.md with workstreams per impact
  tasks    -> writes tasks.md derived from plan
  analyze  -> writes review.md, checks impact coverage
  promote  -> drains proposed-updates.md (audit policy lands in policies.md)
  implement-> runs code/tests, updates capability file, runs build/test
}
  |
  v
status: COMPLETE
```

If the loop hits a fresh `questions.md` mid-flight, it stops with `Stop reason: fresh-questions`.

---

## Generated documentation

When a spec produces user-visible behavior or system-relevant structure, two doc folders receive output:

```text
docs/users/<feature>.md     - end-user-facing
docs/system/<feature>.md    - architecture / internals
```

Driven by two impact areas: `user-documentation` and `system-documentation`. Diagrams in system docs are written as Mermaid blocks. ERD is mandatory when `data-model` is in impact; other diagrams (component, sequence, state, screen-flow) are recommended when their trigger impacts apply. See [`../IMPACT_ANALYSIS.md`](../IMPACT_ANALYSIS.md) for the full trigger table.

---

## Where to extend the preset

| Extension goal | Touch |
|---|---|
| Add a new change-type vocabulary entry | `IMPACT_ANALYSIS.md` impact-area list |
| Change how a primitive routes | the primitive's command file |
| Change resolution behavior | `RESOLUTION.md` only — primitives reference it |
| Change status inference | `commands/status.md` table |
| Add a new shared mechanism | document it as `<NAME>.md` in preset root, reference from primitives |
| Add a new constitutional principle | `templates/constitution-template-mde-addendum.md` |

---

## Where the preset stays out of the way

The preset does **not**:

- create trace-map files, JSON companion files, or parallel mde folders by default
- enforce a phase model (no BA / App Design / Feature stages)
- maintain a stored status field (status is inferred from artifacts)
- duplicate upstream Spec Kit primitives — it shapes them via the addendum templates
- create branches for capabilities (only specs get branches)

The minimum surface change for a project adopting the preset: install it, accept the constitution addendum, use `specify` / `clarify` / `next` / `status`. Everything else is the same Spec Kit you already know.
