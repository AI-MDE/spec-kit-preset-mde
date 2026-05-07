# Walkthrough Summary

This is the short version of the onboarding walkthrough.

Use it when you want the flow, not the full transcript.

## Mental Model

```text
Memory = truth
Specs  = changes
Next   = progression
Status = visibility
```

## Core Commands

```text
specify   -> create a new spec
clarify   -> refine the current spec
next      -> advance the current spec
status    -> inspect state
```

## Minimal Flow

### 1. Install the preset

Place it under:

```text
.specify/presets/mde/
```

### 2. Create the initial setup spec

```text
setup
```

Expected result:

- `specs/000-initial-project-setup/`
- setup-oriented `Change Impact`
- possible `questions.md`

### 3. Advance setup

```text
next
next
```

Expected result:

- constitution is created or updated
- `.specify/memory/app/` is created and seeded

### 4. Check current state

```text
status
```

### 5. Create a new scoped change

```text
specify employee onboarding feature
```

Expected result:

- a new spec folder
- `spec.md` with:
  - `Change Impact`
  - `Target Areas`
  - `Out of Scope`

### 6. Advance that spec

```text
next
next
next
```

Depending on readiness, `next` routes through:

- `clarify`
- `plan`
- `tasks`

### 7. Refine existing work

If the spec already exists and the work is still the same scoped change:

```text
clarify add an audit trail for onboarding approvals
```

Do not use `specify` to mutate the current spec.

### 8. Start a separate follow-up change

If the work is new, create a new spec:

```text
specify change onboarding UI to form-based layout
```

### 9. Check broader repository state

```text
status project
status capabilities
```

Use these when the repository has multiple specs and shared capabilities.

## What the Walkthrough Teaches

- setup is just another scoped change
- `clarify` is how you refine, not `specify`
- capabilities persist across many specs
- impacts drive planning and task generation
- `status` and `next` should read the same artifact state

## Full Walkthrough

For the longer example, see:

- [../../walkthroughs/employee-onboarding-clean.md](../../walkthroughs/employee-onboarding-clean.md)
