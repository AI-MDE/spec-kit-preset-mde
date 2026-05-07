# mde Spec Addendum

Use this addendum when creating or reviewing `spec.md`.

mde uses one spec shape for all scoped changes.

Do not create trace-map files, implementation-contract files, or JSON companion files by default. Normal Spec Kit artifacts are the contract.

---

## Required Sections

Every spec should include:

```md
# <Spec Name>

## Summary

## Change Impact

## Target Areas

## Out of Scope

## Requirements or Rules

## Acceptance Criteria

## Open Questions
```

Add other sections only when they materially help the spec.

---

## Term Mapping

```text
Spec Kit "feature" -> spec / scoped work item
mde capability  -> durable system behavior
```

If the spec affects a capability, reference the capability file explicitly.

---

## Minimal Scaffold

```md
# <Spec Name>

## Summary
<One short description of the change and why it exists.>

## Change Impact
- <impact-area>: <target path> (<create|modify|remove|propose>)

## Target Areas
- <path or path pattern allowed to change>

## Out of Scope
- <what this spec will not change>

## Capability Links
- <capability file path or none>

## Requirements or Rules
- <business requirement, policy, rule, or design constraint>

## Acceptance Criteria
- [ ] <observable outcome>

## Open Questions
- [ ] <question or none>
```

---

## Change Impact Guidance

`## Change Impact` is mandatory.

Format:

```text
<impact-area>: <target path> (<verb>)
```

Supported verbs:

```text
create
modify
remove
propose
```

Common impact areas include:

```text
constitution
memory
capability
business-requirement
glossary
policy
architecture
design-rule
technology-stack
data-model
contract
code
tests
user-documentation
system-documentation
ui-configuration
```

Apply the highest valid change rule:

```text
constitution
application
capability
spec only
```

Do not hide broader repository changes inside local code-only wording.

---

## Target Area Guidance

`## Target Areas` defines where change is allowed.

Examples:

```text
src/onboarding/
tests/onboarding/
docs/upgrade-notes.md
specs/120-change-onboarding-validation/contracts/
.specify/memory/app/capabilities/employee-onboarding.md
.specify/memory/app/design-rules.md
```

Target areas should:

- cover the declared impacts
- be concrete enough to bound the work
- not be so broad that scope disappears

---

## Capability Guidance

When the spec affects a capability, use explicit file links:

```text
.specify/memory/app/capabilities/<name>.md
```

Example:

```md
## Capability Links
- .specify/memory/app/capabilities/employee-onboarding.md
```

Multiple specs may affect the same capability over time.

---

## Optional Sections

Add these only when they materially help:

```md
## Inputs Reviewed
## Contracts Used or Exposed
## Data Notes
## Proposed Promotions
## Risks
## Dependencies
```

Use `## Proposed Promotions` when the spec discovers broader app-wide knowledge that should move into project memory.

---

## Contract Guidance

When the spec defines callable or depended-on surfaces, place them under:

```text
contracts/api/
contracts/ui/
contracts/events/
contracts/external/
contracts/read-models/
```

Do not create contracts for private helper methods or internal UI components.
