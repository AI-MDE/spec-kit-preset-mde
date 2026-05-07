# mde Checklist Addendum

Use this addendum when creating or reviewing checklists.

Checklists should validate the quality of the current spec and its downstream artifacts, not enforce a phase model.

---

## Spec Readiness Checklist

```md
- [ ] `spec.md` exists
- [ ] `## Change Impact` exists
- [ ] Every impact has a target path
- [ ] `## Target Areas` exists
- [ ] Target areas cover the declared impacts
- [ ] `## Out of Scope` exists
- [ ] Requirements or rules are explicit enough to act on
- [ ] Acceptance criteria are observable
- [ ] Capability links are explicit when capability impact exists
- [ ] Open questions are recorded rather than hidden
```

---

## Planning Checklist

```md
- [ ] `plan.md` reflects the current spec
- [ ] `Change Impact` is confirmed or explicitly refined in planning
- [ ] Every meaningful impact area has a corresponding workstream
- [ ] Project-memory promotions are visible when broader impacts exist
- [ ] Contracts, code, tests, and documentation impacts are identified where relevant
- [ ] Risks and open questions are visible
```

---

## Tasks Checklist

```md
- [ ] `tasks.md` reflects the current plan
- [ ] Every confirmed impact area is covered by tasks
- [ ] Tasks stay within the declared target areas
- [ ] Capability-file work is included when capability impact exists
- [ ] App-memory work is included when broader promotion is required
- [ ] Validation work is included
- [ ] Follow-up work is separated from current-scope work
```

---

## Implementation / Validation Checklist

```md
- [ ] Changed files stay within target areas
- [ ] Declared impacts were actually addressed
- [ ] Required contracts were created or updated
- [ ] Required tests were created or updated
- [ ] Required documentation was created or updated
- [ ] Project-memory updates were completed or explicitly proposed
- [ ] `review.md` reflects current readiness
- [ ] `validation-report.md` exists when validation was performed
```

---

## KISS Checklist

```md
- [ ] No trace-map file was created by default
- [ ] No implementation-contract file was created by default
- [ ] No JSON companion file was created by default
- [ ] No parallel mde artifact folder was created without a real need
- [ ] Existing Spec Kit artifacts carry the responsibility
```
