# mde Question Policy

mde avoids long clarification loops in chat. When the AI has multiple questions, it writes them to a file instead of asking one at a time.

This document defines the question file lifecycle and the rules each command must follow.

---

## Question File Locations

### Per-spec questions

```text
specs/NNN-change-name/questions.md
```

For decisions specific to one spec.

### App-wide questions

```text
.specify/memory/app/questions.md
```

For decisions that affect multiple specs or the application overall (e.g. an architectural choice that emerged during a feature spec).

---

## Required Format

```md
# Questions

## Status
PENDING REVIEW

## Instructions
Review the questions and default answers below. Edit answers directly if needed.
If you say `next`, the proposed answers are treated as approved.

## Questions

### Q1. <question>

Default answer:
<recommended default>

Rationale:
<why this default is reasonable>

Impact if changed:
<which downstream artifacts may change>

Status:
proposed
```

---

## Question Lifecycle

```text
created (proposed)  →  approved  →  applied  →  closed
                    ↘  rejected  →  closed
                    ↘  blocked   →  must be resolved before progress
```

### Statuses

| Status | Meaning |
|---|---|
| `proposed` | Question with a default answer awaiting review. |
| `approved` | Default answer accepted (explicitly or via `next`). Not yet applied to artifacts. |
| `applied` | Approved answer has been written into the spec/plan/memory file it affects. |
| `rejected` | User rejected the default; the question may be reopened with a new default or removed. |
| `blocked` | Cannot proceed without an answer; advancement halts until resolved. |
| `deferred` | Decision postponed; not blocking, but recorded as open. |

---

## Who Owns Each Transition

| Transition | Owner |
|---|---|
| Create question | `specify` (during spec creation), `clarify` (during refinement), or any primitive that surfaces a decision |
| Mark `approved` | `next` when the user types `next` and questions have `status: proposed` |
| Apply approved answer | `next`, immediately after approval — write the answer into the affected spec/plan/memory file |
| Mark `rejected` / `deferred` | User direct edit of `questions.md`, or `clarify` when the user revises the question |
| Mark `blocked` | The primitive that detects the blocker (typically `analyze` or `next`) |
| Close question | The owning primitive after the answer is applied or the question becomes irrelevant |

---

## Approval Rule

When the user says `next` and `questions.md` exists with at least one question at `status: proposed`:

- treat all proposed answers as approved unless the user says they edited or rejected them
- mark each as `approved`, then immediately apply each answer to its target artifact
- mark each as `applied` once written
- update the file's top-level `## Status` from `PENDING REVIEW` to `APPROVED` once all questions are applied or otherwise closed

If the user explicitly says they edited or rejected specific questions, only the remaining proposed ones are approved.

---

## Application Rule

An approved answer must land somewhere durable. Possible targets:

- `spec.md` — for decisions about scope, impact, target areas, requirements
- `plan.md` — for execution decisions
- `tasks.md` — for sequencing or scope decisions
- `.specify/memory/app/<file>.md` — for app-wide decisions
- `.specify/memory/app/proposed-updates.md` — when the application target is itself pending promotion

Do not leave an approved question with no application target. If there is no obvious target, treat the question as a sign that the spec is incomplete and route to `clarify`.

---

## Loop Prevention Rule

Once questions are written to `questions.md`, do not re-ask them in chat. Point the user to the file.

If the user asks the same question again in chat, refer them back to the existing entry rather than creating a duplicate.

---

## Staleness Rule

If the spec changes materially after questions were approved, previously-applied answers may become stale. Treat them as still-applied until something explicitly invalidates them. Do not silently re-ask.

`clarify` may surface that a previously-resolved question now needs re-confirmation; in that case, add a new question with a reference to the prior decision.

---

## Final Rule

Questions are editable artifacts with a defined lifecycle. Chat is for notification and decisions, not for long question loops.
