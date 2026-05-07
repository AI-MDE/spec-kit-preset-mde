# mde Terminology

Core vocabulary used by the mde preset.

---

## Term Mapping

```text
Spec Kit "feature" -> spec / scoped work item
mde capability  -> durable system behavior
```

Spec Kit uses `feature` for what this preset usually treats as a spec-shaped unit of work. mde uses `capability` for the persistent system concept.

---

## Spec

A spec is a scoped change package.

```text
specs/NNN-change-name/
```

It describes a bounded unit of work: intent, questions, plan, tasks, and validation.

---

## Capability

A capability is a persistent system behavior or responsibility.

Examples:

- Employee Onboarding
- Authentication
- Approval Workflow

Capabilities live in project memory:

```text
.specify/memory/app/capabilities/
```

Multiple specs may affect the same capability over time.

---

## Project Memory

Persistent project-wide application knowledge lives under:

```text
.specify/memory/app/
```

Typical contents:

- capabilities
- business requirements
- glossary
- architecture
- data model
- design rules
- policies
- technology stack
- proposed updates

---

## Change Impact

`Change Impact` is the list of repository areas a spec affects.

It is written in `spec.md` as:

```md
## Change Impact
```

Example:

```text
- capability: .specify/memory/app/capabilities/employee-onboarding.md (modify)
- code: src/onboarding/ (modify)
- tests: tests/onboarding/ (add)
```

---

## Status Modes

`status` reads artifact state for the current spec.

`status project` scans for stale or incomplete specs.

`status capabilities` scans for incomplete or stale capabilities.
