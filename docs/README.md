# Preset Docs

Human reference material for the mde preset, ordered for a new reader.

> **Note for AI agents:** this folder is human reference only. Do not read from `docs/` when deciding behavior. Authoritative sources live at the preset root: [`RESOLUTION.md`](../RESOLUTION.md), [`QUESTION_POLICY.md`](../QUESTION_POLICY.md), [`IMPACT_ANALYSIS.md`](../IMPACT_ANALYSIS.md), and [`commands/`](../commands/). See Principle X (Preset Authority) in the constitution addendum.

## Start here

- [getting-started.md](getting-started.md)
  Install the preset, write your first spec, and watch `next` advance it.
- [faq.md](faq.md)
  Short answers to common usage questions, including how to redo a spec safely.
- [walkthrough-summary.md](walkthrough-summary.md)
  The short version of the onboarding walkthrough.

## Concepts

- [terminology.md](terminology.md)
  Core vocabulary and term mapping.
- [capabilities-vs-specs.md](capabilities-vs-specs.md)
  Why capabilities and specs are separate concepts.
- [`../IMPACT_ANALYSIS.md`](../IMPACT_ANALYSIS.md) *(authoritative — agents read this)*
  How impact is analyzed and how `Change Impact` is written.

## Reference (generated / human-only)

- [rules.md](rules.md)
  Generated index of every rule in the preset. Regenerate with `tools/list-rules.js`.
- [preset-validation.md](preset-validation.md)
  Post-install validation checklist consumed by `tools/validate-preset.js` and human reviewers.
- [runtime-context-loading.md](runtime-context-loading.md)
  Which files load into agent context, when, and why.

## Examples

- [user-cases.md](user-cases.md)
  Concrete `specify` scenarios for greenfield, brownfield, refactor, policy, and tech-stack changes.
- [walkthrough-summary.md](walkthrough-summary.md)
  Compressed walkthrough flow and command choices.
- [../../walkthroughs/it-consulting-org.md](../../walkthroughs/it-consulting-org.md)
  Full replay from a real session.

## Extending the preset

- [architecture.md](architecture.md)
  How commands, primitives, shared mechanisms, and templates fit together.
- [../RESOLUTION.md](../RESOLUTION.md)
  Active-spec resolution algorithm.
- [../QUESTION_POLICY.md](../QUESTION_POLICY.md)
  Question lifecycle and ownership.
