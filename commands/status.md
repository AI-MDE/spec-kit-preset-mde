# mde: Status

Provide a read-only summary of project state.

`status` must not modify files unless explicitly asked.

---

## 1. Modes

`status` supports three modes:

### `status`

Fast default mode. Report on the current spec only.

Answer:

```text
What is the active spec?
What is its current status?
What is blocked?
What should happen next?
```

### `status project`

Project scan mode. Report on stale specs across the workspace.

Answer:

```text
Which specs look stale, blocked, abandoned, or incomplete?
Which specs need attention first?
```

### `status capabilities`

Capability scan mode. Report on incomplete capabilities.

Answer:

```text
Which capabilities look incomplete, stale, or inconsistent?
What follow-up specs are likely needed?
```

---

## 2. Shared Status Model

`status` and `next` must use the same artifact-based status model.

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

### Inference rules

Apply in order. The first matching rule is the spec's status.

| Status | Trigger |
|---|---|
| `BLOCKED` | `review.md` exists and lists unresolved blockers, OR `questions.md` has questions with `status: blocked`. |
| `COMPLETE` | `validation-report.md` exists and reports success, OR `review.md` has `Assessment: READY` and `tasks.md` shows all tasks complete. |
| `IN PROGRESS` | `tasks.md` shows at least one task complete and at least one task open, AND no blockers visible. |
| `IMPLEMENTATION READY` | `plan.md` and `tasks.md` exist and are not stale (mtime ≥ `spec.md`), AND no `BLOCKED` trigger applies. |
| `NEEDS REVIEW` | `tasks.md` exists but `review.md` is missing or stale (mtime < `tasks.md`), AND no implementation evidence yet. |
| `TASKED` | `tasks.md` exists, `plan.md` exists, but no review or implementation evidence yet. |
| `PLANNED` | `plan.md` exists, no `tasks.md` yet. |
| `READY FOR PLAN` | `spec.md` has `## Change Impact` with target paths AND `## Target Areas` AND `## Out of Scope`, AND no `questions.md` OR all questions have `status: approved`, AND no `plan.md` yet. |
| `READY WITH QUESTIONS` | Same as READY FOR PLAN but `questions.md` has at least one question with `status: proposed`. |
| `DRAFT` | `spec.md` exists but `## Change Impact` is missing OR any impact lacks a target path OR `## Target Areas` is missing. |

Staleness uses file mtime. A downstream artifact (`plan.md`, `tasks.md`, `review.md`) is **stale** when its mtime is older than the spec.md it depends on.

Use conservative inference. Do not claim completion unless the artifacts clearly support it.

---

## 3. Sources

Default mode reads from:

```text
.specify/memory/app/work-state.md
.specify/memory/app/questions.md
.specify/memory/app/proposed-updates.md
active specs/NNN-*/
```

Project mode also scans:

```text
all specs/*/
git branch context when relevant
```

Capabilities mode also scans:

```text
.specify/memory/app/capabilities/
specs that reference each capability
relevant docs, contracts, tests, and source areas when they are easy to identify
```

---

## 4. Default Mode: `status`

Summarize:

- active spec folder
- overall status
- spec status
- impact summary
- questions status
- plan status
- tasks status
- review status
- blockers
- pending questions
- pending app updates
- next suggested action

The default mode should be fast. Prefer the current spec only.

---

## 5. Project Mode: `status project`

Scan all visible specs and report stale or incomplete ones.

A spec is worth flagging when signals suggest:

- `spec.md` exists but downstream work never progressed
- questions remain unresolved for too long
- plan exists but tasks do not
- tasks exist but no review or implementation evidence appears
- review shows blockers or readiness gaps
- the branch or spec appears abandoned compared to more recent work

Report:

- stale specs
- blocked specs
- incomplete specs
- specs that likely need `clarify`
- specs that likely need `plan`, `tasks`, `analyze`, or `implement`

Keep it short. Prioritize the most actionable items first.

---

## 6. Capability Mode: `status capabilities`

Scan capabilities for visible incompleteness or drift.

A capability is worth flagging when signals suggest:

- it has related specs but no clear current capability file state
- documentation, contracts, tests, and code appear out of sync
- multiple specs changed it but app memory was not promoted
- follow-up work is clearly implied but missing
- terminology or dependency drift is visible

Report:

- incomplete capabilities
- stale capabilities
- capabilities with buried app-wide knowledge
- likely follow-up specs

This mode is read-only backlog intelligence, not planning.

---

## 7. Output Format

### Default

```text
Mode: status
Active spec: <folder or none>
Status: <status>
Impact summary: <short summary of affected areas, or none>
Blockers: <summary or none>
Pending questions: <summary or none>
Pending app updates: <summary or none>
Next: <suggested next action>
```

### Project

```text
Mode: status project
Stale specs:
- <spec>: <reason>

Blocked specs:
- <spec>: <reason>

Incomplete specs:
- <spec>: <next likely action>
```

### Capabilities

```text
Mode: status capabilities
Incomplete capabilities:
- <capability>: <reason>

Likely follow-up specs:
- <spec idea>
```

---

## 8. Rule

`status` is read-only.

It should describe state, not silently repair it.
