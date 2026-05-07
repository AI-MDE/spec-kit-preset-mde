# Use Cases

Concrete scenarios for `specify`.

Command split:

- `specify` creates a new spec
- `clarify` refines the current spec
- `next` advances the current spec
- `status` reports inferred state

When user prompts say `feature`, treat that as upstream Spec Kit wording. In mde, the durable system concept is a `capability`.

---

## 1. Initial Project Setup

**Command:**

```text
specify
```

**After:**

Create `specs/000-initial-project-setup/`, generate `questions.md`, and prepare project memory under `.specify/memory/app/`.

Typical impacts:

```text
constitution      -> .specify/memory/constitution.md (create)
memory            -> .specify/memory/app/* (create or propose)
```

---

## 2. New Capability

**Command:**

```text
specify employee onboarding feature
```

**After:**

Create a new spec folder for the change and identify the target capability.

Typical impacts:

```text
capability  -> .specify/memory/app/capabilities/employee-onboarding.md (create)
code        -> src/onboarding/ (create)
tests       -> tests/onboarding/ (create)
contract    -> specs/NNN-employee-onboarding/contracts/ (create)
```

---

## 3. Technology Stack Change

**Command:**

```text
specify change technology stack to FastAPI + React
```

Typical impacts:

```text
technology-stack  -> .specify/memory/app/technology-stack.md (modify)
code              -> src/ (modify)
tests             -> tests/ (verify)
documentation     -> docs/upgrade-notes.md (create)
```

---

## 4. Architecture or Rule Change

**Command:**

```text
specify add layered application rule
```

Typical impacts:

```text
design-rule   -> .specify/memory/app/design-rules.md (modify)
architecture  -> .specify/memory/app/architecture.md (modify)
```

---

## 5. Refactor or Bug Fix

**Command:**

```text
specify refactor onboarding service to use repository pattern
```

or

```text
specify fix onboarding validation timeout
```

Typical impacts:

```text
code   -> src/onboarding/ (modify)
tests  -> tests/onboarding/ (verify or add regression)
```

If the pattern becomes app-wide, the spec may also propose application-level promotions.
