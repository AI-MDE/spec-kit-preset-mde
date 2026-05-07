# mde: Setup

Thin alias for first-run project scaffolding.

```text
setup = specify initial project setup
```

`setup` does not have its own logic. It defers to `specify` with a setup-oriented description. See [`specify.md`](specify.md) section 3 (First-Run Behavior) for the actual mechanics — including the greenfield / brownfield branch.

If a setup-related spec already exists, `setup` should not run. Tell the user to:

- use `clarify` to refine the existing setup spec, or
- use `specify` for a new setup-related sibling spec

`setup` is a convenience trigger, not a parallel workflow. Adding behavior here is a smell — extend `specify` instead.
