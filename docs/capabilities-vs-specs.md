# Capabilities vs Specs

mde keeps two concepts separate:

```text
capability = persistent system behavior
spec       = controlled scope of work
```

When comparing this model to upstream Spec Kit terminology:

```text
Spec Kit "feature" -> spec / scoped work item
mde capability  -> persistent system capability
```

---

## Capability

A capability is something the application has or does.

Examples:

- Employee Onboarding
- Authentication
- Approval Workflow

Capabilities live in:

```text
.specify/memory/app/capabilities/
```

---

## Spec

A spec is the bounded work performed against the system.

Examples:

- create the onboarding capability
- revise onboarding validation
- add an audit trail
- change onboarding UI

Specs live in:

```text
specs/NNN-change-name/
```

Branches map to specs, not to capabilities.

---

## Relationship

A spec may:

- create a new capability
- modify an existing capability
- revise architecture or policy
- refactor implementation
- target no capability at all

Multiple specs may affect the same capability over time.

Example:

```text
Capability:
  Employee Onboarding

Specs:
  specs/010-create-employee-onboarding/
  specs/120-change-onboarding-validation/
  specs/160-add-onboarding-audit-trail/
```

---

## Why It Matters

Separating capability from spec makes it easier to:

- keep a durable application capability catalog
- detect app-wide promotions
- manage many specs against one system capability
- keep branches tied to current work rather than permanent business concepts
