# Runtime Context Loading

What gets loaded into the AI agent's context at runtime, when, and why. Human reference material — agents must not read this file at runtime per Principle X.

## Three layers

Every Spec Kit command in an mde-aware project assembles its runtime context from three independent sources:

1. **Engine session start** — what the AI tool itself auto-loads when the editor opens.
2. **Sync hook** — what `speckit.mde.sync` reads on every fire (before each Spec Kit command and at the start of every wrapper).
3. **Per-command loads** — what each preset command's `Required Inputs` section reads when invoked.

The layers are additive: a single `/speckit-implement` invocation may touch all three.

---

## Layer 1 — Engine session start

| File | When | Why |
|---|---|---|
| `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` | Once, when the editor opens the project | Engine convention. Auto-loaded by Claude Code, Codex, or Gemini Code Assist. Contains the mde marker block, which **names** preset entry points but doesn't auto-follow them — references are read lazily by the agent only when something requires it. |

At session start the agent has roughly the marker block (a few hundred bytes) and nothing else mde-specific.

---

## Layer 2 — Sync hook

`speckit.mde.sync` fires before every `/speckit-*` command and at the start of every `/speckit.mde.*` wrapper. Its responsibilities are marker reconciliation, log bootstrap, and session-scoped context priming.

Sync's context-load rule (session-local):

| Condition | Loads | Size |
|---|---|---:|
| First sync fire of this session, constitution ratified | `RESOLUTION.md` + `constitution.md` | ~5.6k |
| First sync fire of this session, constitution still a placeholder | + `templates/constitution-template-mde-addendum.md` (fallback) | ~10.4k |
| A marker target was `injected` or `refreshed` this fire (recovery from drift / fresh install) | same set as above | ~5.6k–10.4k |
| Subsequent fires in the same session, no marker reconciliation needed | none | **0** |

Files **not** loaded by the hook:

- `IMPACT_ANALYSIS.md` — read per-command by `specify`, `clarify`, `plan`.
- `QUESTION_POLICY.md` — read per-command by `specify`, `clarify`, `next`.
- [`docs/rules.md`](rules.md), [`docs/preset-validation.md`](preset-validation.md) — human reference; never loaded into agent context.

The hook detects "first fire of session" by checking conversation history for prior tool-call results that read the entry set. After the first fire, the relevant content is already in history; subsequent fires skip the load entirely so duplicate tool-call outputs don't accrete.

---

## Layer 3 — Per-command loads

Each preset command file declares its own dependencies in a `Required Inputs` (or equivalent) section. The agent reads them when the command runs.

| Command | Always loads | Conditionally loads |
|---|---|---|
| `specify` (first-run / setup) | `constitution.md`, `app/`, profile + defaults files | `proposed-updates.md`, `work-state.md` |
| `clarify` | `spec.md`, `app/capabilities/<name>.md`, `app/design-rules.md` | `questions.md` |
| `plan` | `spec.md` | `data-model.md`, `contracts/`, `constitution.md`, `app/capabilities/<name>.md`, `app/*.md` referenced by Change Impact |
| `tasks` | `spec.md`, `plan.md` | `contracts/`, `data-model.md`, capabilities, app memory referenced by Change Impact |
| `analyze` | `spec.md`, `plan.md`, `tasks.md` | capabilities, app memory referenced by Change Impact |
| `checklist` | `spec.md`, `plan.md`, `tasks.md` | capabilities, app memory referenced by Change Impact |
| `implement` | `spec.md`, `plan.md`, `tasks.md` | `checklists/`, `contracts/`, `data-model.md`, `review.md`, capabilities, source files inside Target Areas |
| `constitution` | `constitution.md`, `app/` | — |
| `status` | `work-state.md`, `questions.md`, `proposed-updates.md`, `capabilities/` | — |
| `next` | (delegates to whichever command is next) | `proposed-updates.md` (drain step); references `QUESTION_POLICY.md` |

The `setup` command is a thin alias for first-run `specify`; its load profile matches `specify` row 1.

---

## Caching considerations

Anthropic's prompt cache has a 5-minute TTL and caches the conversation prefix (system prompt → conversation history → tool-call results) up to the latest user message. Files read by tool calls in earlier turns become part of that prefix on later turns.

Three implications for mde-aware commands:

### 1. Repeated reads are cheap when warm

Within a 5-minute window of activity, files read on turn N are largely cache-hit content on turn N+1. So the wall-clock byte sizes in the tables above are not the same as LLM token cost — most of the bytes ride for free on a warm cache. Steady-state savings from a slim load list matter most on the **first** command of a session and after **>5 minutes of idle**, not in the middle of an active session.

### 2. The session-local sync rule is cache-aware

Sync's load fires once per session, not once per command, precisely because the second-and-subsequent loads would duplicate content already in conversation history. Skipping them keeps history compact and the cached prefix stable. A hypothetical "load on every fire" rule would still benefit from caching, but it would inflate conversation history with redundant tool-call outputs and create more cache breakpoints — every additional read is one more place where content drift could invalidate the cache.

### 3. Cache-breakers to avoid

| Action | Effect on cache |
|---|---|
| Sync writes to `logs/<YYYY-MM-DD>.md` | **Safe.** No command reads `logs/` into context, so log writes never invalidate the cache. |
| Sync writes to `specs/NNN-*/session-log.md` | **Safe** for the same reason — session logs aren't read into agent context. |
| Marker reconciliation writes to `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` | **Safe within the current session** — the engine's session-start snapshot of the auto-load file stays in memory; mid-session writes only take effect on the next session. |
| A future command that reads `logs/` into context | **Cache-breaking.** Every sync write to today's log would invalidate the cache from that read forward. Treat log files as write-only from the agent's perspective. |
| Editing a file the agent has already read this session (e.g. `spec.md`) | **Cache-breaking from that point forward.** Unavoidable for spec mutations, but worth knowing — bunching reads-then-writes per turn keeps the cost contained. |

### 4. Implication for the `size` log field

With caching in play, the `size` field in session logs overstates real token cost on warm-prefix turns and understates it after idle. It is a volume proxy, not a billing figure. The constitution addendum already says this; treat it as a reminder rather than a metric to optimize.

---

## Cost framing for the `size` field

Constitution Principle IX records a `size` field per log entry, summing the *visible* contributors the agent observed: user prompt + files read this turn + tool outputs (grep, bash, web fetch). It excludes system prompt, conversation history, and tool definitions.

For an mde-aware command, the size budget breaks down as:

```text
user prompt              (varies — usually <1k)
+ Layer 1 marker block   (already loaded at session start, not counted again)
+ Layer 2 sync load      (0 in steady state, 5.6k–10.4k on first fire after install/refresh)
+ Layer 3 command load   (varies by command — see table)
+ tool outputs           (grep, bash, web — varies)
= visible size
```

If a logged size figure exceeds the project's total bytes-on-disk, the agent has miscounted (most commonly by including system prompt or conversation history). Use that as a guardrail when reviewing logs: write `NA` rather than an inflated estimate.

---

## See also

- [`rules.md`](rules.md) — generated rule index (human reference only).
- [`preset-validation.md`](preset-validation.md) — post-install validation checklist.
- [`../RESOLUTION.md`](../RESOLUTION.md) — active-spec resolution.
- [`../templates/constitution-template-mde-addendum.md`](../templates/constitution-template-mde-addendum.md) — Principles IX (Session Accountability) and X (Preset Authority).
- [`architecture.md`](architecture.md) — broader picture of how commands, primitives, and templates fit together.
