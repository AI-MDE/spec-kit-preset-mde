# Getting Started

Install the preset, write your first spec, and watch `next` chain through implementation. About 5 minutes.

---

## Prerequisites

- Spec Kit installed (`uv tool install specify-cli --from git+https://github.com/github/spec-kit.git`)
- An AI agent that speaks Spec Kit slash commands (Claude Code, Codex, etc.)

---

## 1. Install the preset

In your project root:

```bash
specify init my-app
cd my-app
specify preset add mde
```

Or, manually, copy `preset/` into `.specify/presets/mde/`.

After install:

```text
.specify/presets/mde/
  preset.yml
  README.md
  RESOLUTION.md
  QUESTION_POLICY.md
  IMPACT_ANALYSIS.md
  docs/
  commands/
  templates/
  defaults/
```

---

## 2. First spec — let `specify` do the bootstrap

In your AI agent:

```text
specify
```

With no arguments on a fresh project, `specify` creates the initial setup spec:

```text
specs/000-initial-project-setup/
  spec.md
  questions.md   (if scope is vague)
  session-log.md
```

If the project already has source code, `specify` infers proposed memory from it (capabilities from routes, tech stack from manifests, architecture from layout). Inferences land in `.specify/memory/app/proposed-updates.md` for review.

If the project is empty, `specify` writes six setup questions to `questions.md` and a fillable `## Application Profile` section in `spec.md` — checkbox lists of architecture rules (AR-###) and design rules (DR-###) pre-populated from the chosen profile.

The questions in `questions.md`:

```text
Q1. Architecture profile?               (default: web-monolith)
      - web-monolith    server-rendered + REST API, internal business apps
      - microservices   distributed services with bounded contexts
      - event-driven    event sourcing or pub/sub patterns
      - api-only        headless backend, no UI in this codebase
      - cli-tool        command-line tool, no UI/DB
Q2. Project name?                       (default: repo folder name)
Q3. Business domain?                    (default: general business application)
Q4. Primary users?                      (default: internal staff)
Q5. Compliance/regulatory context?      (default: none)
Q6. Accept default AR/DR rule set?      (default: accept)
```

Saying `next` accepts all defaults. Q1 picks the profile (changes the AR/DR rules pre-populated from `preset/defaults/profiles/<your-choice>.md`). Q2–Q5 fill the constitution (`[PROJECT_NAME]`, Domain Context, Constraints). Q6 accept-or-reject covers the entire AR/DR set as one decision; per-rule editing happens in `spec.md`'s Application Profile by unchecking or editing individual lines before `next`.

---

## 3. Edit the questions and run `next`

Open `specs/000-initial-project-setup/questions.md`, edit the proposed answers if needed, then:

```text
next
```

`next` chains through:

```text
clarify -> plan -> tasks -> analyze -> promote -> implement
```

In one invocation. It stops at the first of:

- `COMPLETE` (everything ran green)
- `BLOCKED` (a real problem)
- fresh questions surfaced this turn (you must review)
- build / test failure

For initial setup, `next` typically promotes the proposed memory into `.specify/memory/app/`:

```text
.specify/memory/
  constitution.md
  app/
    architecture.md
    business-requirements.md
    capabilities/
    data-model.md
    design-rules.md
    glossary.md
    policies.md
    technology-stack.md
```

---

## 4. Your first feature spec

```text
specify employee onboarding feature
```

Creates `specs/010-employee-onboarding/`. The spec's `## Change Impact` is inferred from existing project memory + your description:

```md
## Change Impact
- capability: .specify/memory/app/capabilities/employee-onboarding.md (create)
- code: src/onboarding/ (create)
- tests: tests/onboarding/ (create)
- contract: specs/010-employee-onboarding/contracts/ (create)
```

Then:

```text
next
```

The loop runs to completion or stops at the first fresh `questions.md`. After review:

```text
next
```

And you have a working scaffolded feature with code, tests, contracts, capability file, and validation evidence.

---

## 5. Inspect state

```text
status
```

Shows the active spec and what `next` will do next. Or:

```text
status project
```

Scans all specs for stale or incomplete ones. Or:

```text
status capabilities
```

Reviews the capability catalog for drift.

---

## What you should see in your repo

After steps 1-4:

```text
my-app/
  .specify/
    memory/
      constitution.md
      app/
        capabilities/employee-onboarding.md
        architecture.md
        ...
  specs/
    000-initial-project-setup/
    010-employee-onboarding/
  logs/
    2026-05-06.md
  src/
    onboarding/
  tests/
    onboarding/
  package.json   (with build/test/start/db scripts wired up)
```

The capability file in `.specify/memory/app/capabilities/` is the durable description of what the system *does*. The spec folder is the audit trail of *how this change happened*. Future specs that refine onboarding will modify the capability file and accumulate as new spec folders alongside `010-`.

---

## What to read next

If you want the model:

- [terminology.md](terminology.md) — capability vs spec, change impact, target areas
- [capabilities-vs-specs.md](capabilities-vs-specs.md) — why they're separate

If you want examples:

- [user-cases.md](user-cases.md) — `specify` scenarios (greenfield, brownfield, refactor, policy, tech-stack change)
- [../../walkthroughs/it-consulting-org.md](../../walkthroughs/it-consulting-org.md) — full real-session walkthrough

If you want to extend the preset:

- [architecture.md](architecture.md) — how the pieces fit
- [../RESOLUTION.md](../RESOLUTION.md), [../QUESTION_POLICY.md](../QUESTION_POLICY.md) — shared mechanisms
- [preset-validation.md](preset-validation.md) — post-install validation checklist (human reference)

---

## Common first-run issues

**"What spec am I on?"** Run `status`. The active-spec resolution rule is in [`../RESOLUTION.md`](../RESOLUTION.md).

**`next` halted at fresh questions.** Open `questions.md`, edit answers, run `next` again. Per [`../QUESTION_POLICY.md`](../QUESTION_POLICY.md), proposed answers count as approved on the next `next`.

**Build/test failed during `next`.** That's a `BLOCKED` stop. Fix the failing command, then `next` to resume.

**Spec changed and downstream artifacts look wrong.** That's a staleness state. `analyze` and `implement` refuse to run against stale artifacts; route through `plan` and `tasks` first to refresh.
