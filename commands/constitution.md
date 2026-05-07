# mde: Constitution

Create or refine project-level guardrails for mde usage.

The constitution is the highest valid level of change. When the same problem keeps reappearing in specs, plans, or tasks, fix the governing rule here.

---

## 1. Scope

The constitution lives at:

```text
.specify/memory/constitution.md
```

This command is project-level.

It does not create or modify spec folders by default.

---

## 2. Purpose

Use `constitution` to define or refine project-wide rules for:

- scoped change control
- project memory
- capability handling
- contracts
- controlled AI context
- highest valid change
- validation and evidence expectations

Do not use it to encode one spec's local design or implementation detail.

---

## 3. Inputs

Load:

```text
.specify/memory/constitution.md
templates/constitution-template-mde-addendum.md
user instruction
```

When relevant, also inspect:

```text
.specify/memory/app/
current recurring spec issues
```

Use project reality to inform the rule, but keep the constitution concise and durable.

---

## 4. Universal Rules

- Keep constitution rules short, declarative, and enforceable.
- Do not duplicate one spec's local content.
- Do not duplicate detailed app-memory content unless it is truly constitutional.
- Do not invent rules the user did not ask for.
- When a proposed rule conflicts with an existing rule, ask before overwriting.

---

## 5. What the Constitution Should Cover

Typical sections include:

```md
# Project Constitution

## Artifact Contract

## Scoped Change Control

## Project Memory

## Capability Rules

## Contracts Rule

## Highest Valid Change Rule

## Controlled AI Context

## Validation and Evidence

## Naming and Numbering Conventions
```

Not every project needs every section, but these are the normal areas of concern.

---

## 6. Default Principles

The bundled addendum provides the normal starting principles:

- normal Spec Kit artifacts are the contract
- specs are scoped changes
- durable application knowledge belongs in project memory
- capabilities are persistent system behaviors
- no trace-map or JSON sidecar files by default
- contracts represent callable or depended-on surfaces
- change should happen at the highest valid level
- validation evidence should be recorded

Use these as defaults unless the user overrides them.

---

## 7. Must Not Include

Do not put these here unless they are truly project-wide non-negotiable rules:

- one spec's local acceptance criteria
- one spec's local file paths
- one spec's implementation tasks
- detailed architecture notes that belong in project memory instead
- local contract details that belong in a spec folder

---

## 8. Output Style

When reporting what `constitution` is doing:

- state plainly that you are updating the project constitution
- list which rules were added, refined, or removed
- name any conflicts you resolved or skipped

---

## 9. Final Rule

The constitution defines what is true everywhere.

Specs, plans, tasks, and implementations should conform to it.
