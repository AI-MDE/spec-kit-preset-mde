# mde Constitution Addendum

Use this addendum to strengthen the project constitution for mde.

## mde Principles

### I. Spec Kit First

The project uses normal Spec Kit artifacts as the contract:

```text
spec.md
plan.md
data-model.md
contracts/
tasks.md
checklists/
```

mde does not create a parallel lifecycle by default.

### II. Scoped Change Control

Work is managed as specs:

```text
specs/NNN-change-name/
```

Each spec must declare:

- `Change Impact`
- `Target Areas`
- `Out of Scope`

### III. Durable System Knowledge

Durable application knowledge belongs in project memory:

```text
.specify/memory/app/
```

Capabilities are persistent system behaviors and should live in:

```text
.specify/memory/app/capabilities/
```

Multiple specs may affect the same capability over time.

### IV. No Heavy Trace-Map System by Default

The project must not create trace-map files by default.

The project must not create separate implementation-contract files by default.

The project must not create JSON companion files by default.

Use the normal Spec Kit folder structure unless a real project need justifies an additional artifact.

### V. Contracts Folder Rule

The `contracts/` folder contains callable surfaces and obligations:

```text
api/
ui/
events/
external/
read-models/
```

A page, route, or view belongs in `contracts/ui/` when another flow or spec depends on it.

Internal helpers and private components are not contracts.

### VI. Highest Valid Change Rule

When change is needed, apply it at the highest valid level:

```text
constitution
application
capability
spec only
```

Do not hide broader decisions inside local implementation work.

### VII. Controlled AI Context

The AI engine should not read or modify the whole repository by default.

Tasks should identify relevant files and allowed write areas when implementation scope is non-trivial.

The AI engine must not modify unrelated files or create new work outside the current spec.

### VIII. Validation Gate Rule

Implementation is not complete until:

- declared impacts are addressed
- tasks, contracts, tests, and docs are updated when required
- project-memory promotions are completed or explicitly proposed
- validation evidence is recorded when validation is performed

Changed files, gaps, and follow-ups should be recorded in `tasks.md`.

Validation results should be recorded in `validation-report.md` when validation is performed.

### IX. Session Accountability

Every material user interaction and AI action is logged in two places:

1. The chronological central log: `logs/<YYYY-MM-DD>.md`
2. The per-spec audit trail: `specs/NNN-change-name/session-log.md` (only when the interaction targets a specific spec)

Spec-bound interactions are mirrored to both. Non-spec interactions (`status project`, app-memory edits with no spec target) appear only in the central log.

Each entry captures:

- timestamp (ISO 8601 if available, else `NA`)
- actor (`user` or `assistant`)
- interaction (the user prompt, or a short label for an AI action)
- outcome (one line)
- tokens (count if available, else `NA`)
- size (visible input/output character counts, e.g. `in ~19.6k / out 4.8k`)

The `size` field is a volume proxy, not a cost figure. It sums the *visible* contributors the agent can observe: user prompt + files read this turn + grep/bash/web-fetch outputs for input, AI response text for output. It excludes system prompt, conversation history, and tool definitions. Mark with `(visible only)` if the distinction matters in context.

Use `NA` for unavailable values. Do not pad with explanatory prose.

### X. Preset Authority

Any AI agent working in this repository MUST consult the mde preset before taking any action that creates, modifies, or removes files. This applies whether work is invoked via Spec Kit slash commands (`/speckit.*`) or via open-ended chat.

The preset's authoritative sources are:

```text
.specify/presets/mde/RESOLUTION.md
.specify/presets/mde/QUESTION_POLICY.md
.specify/presets/mde/IMPACT_ANALYSIS.md
.specify/presets/mde/commands/
.specify/presets/mde/templates/
.specify/presets/mde/defaults/
```

`.specify/presets/mde/docs/` is **human reference material only** — getting-started guides, FAQ, walkthroughs, terminology. Agents must not read from `docs/`. Any rule that needs to influence agent behavior must live in one of the authoritative sources above, not in `docs/`.

The agent MUST default to the preset's model: capability/spec separation, `## Change Impact` and `## Target Areas` on every spec, `specify`/`clarify`/`next`/`status` command flow, propose-then-promote for app-memory edits, and read-before-decide for impact verbs.

If the preset is installed but unreadable, do not proceed — surface the access problem instead of guessing.
