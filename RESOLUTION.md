# Active Spec Resolution

How mde primitives identify which spec to operate on.

This rule is shared by every primitive that targets one spec: `clarify`, `plan`, `tasks`, `analyze`, `checklist`, `implement`, and `next`. It is not used by `specify` (creation) or by `status` in project/capabilities mode.

---

## Resolution Order

Resolve in this order. Stop at the first match.

1. **Explicit folder** — the user named a `specs/NNN-change-name/` folder in the request.
2. **Branch match** — the current Git branch's leading numeric prefix matches exactly one `specs/NNN-*/` folder. Example: branch `010-employee-onboarding` ↔ folder `specs/010-employee-onboarding/`.
3. **Single in-flight spec** — exactly one spec is in flight (see below). Use it.

---

## When None of the Rules Match

- **Multiple candidates at step 2 or 3** — list them, ask the user which spec to use. Do not guess.
- **No spec exists at all** — tell the user to run `specify`.
- **Spec folder named but missing** — tell the user the folder was not found; do not create one.

A primitive must never invent or create a spec folder.

---

## "In-Flight" Definition (for step 3)

A spec is **in flight** when both are true:

- it is not marked `COMPLETE` in `review.md` or `validation-report.md`, and
- `spec.md` is newer than any `validation-report.md` in the same folder, *or* no `validation-report.md` exists.

If two specs both qualify, prefer the one whose folder name matches the current branch's numeric prefix. If still ambiguous, ask.

---

## Branch ↔ Folder Convention

The folder name is authoritative. The branch name is convenience.

When the branch numeric prefix and folder numeric prefix disagree, prefer the folder. Tell the user the branch may be on the wrong spec.

---

## Final Rule

A primitive's job is to operate on *one* spec. If which spec isn't obvious by these rules, ask. Never silently mutate the wrong spec.
