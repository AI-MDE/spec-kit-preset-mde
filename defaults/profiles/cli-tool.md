# Profile: cli-tool

Command-line tool, library, or developer utility. Single binary or script. No UI, often no database, often single-user.

Use this profile for CLI utilities, build tools, dev tooling, scripts, libraries, or anything where the primary interface is a terminal.

---

## Architecture rules

Apply (from `architecture.md`):

- AR-005 Command-Query Separation — at the function level

Drop:

- AR-001 Modular Monolith — irrelevant; a CLI is one process by definition
- AR-002 Layered Application — over-engineered for most CLIs; use plain modules
- AR-003 MVC + REST API — no UI, no API
- AR-004 Repository Pattern — no persistence boundary in most CLIs

## Design rules

Apply: DR-001 (validation — but at command-input layer rather than domain), DR-006 (logging — to stderr or a log file), DR-015 (testing).

Drop:

- DR-002..DR-005, DR-007, DR-008 — no persistence, no aggregates, no audit trail
- DR-009..DR-014 — no UI
- DR-016 — replace with `--help` text and `README.md` rather than full feature docs

## Profile-specific additions

- **`--help` is the manual:** every command and subcommand must have inline help. The CLI's own self-documentation replaces formal user docs.
- **Exit codes matter:** 0 for success, non-zero with documented meanings for failure modes.
- **stderr vs stdout discipline:** machine-readable output to stdout, status/log to stderr. Don't mix.
- **No interactive prompts unless explicitly requested:** support `--yes`, `--no-interactive` flags. CLIs often run in scripts.
- **Idempotency where possible:** rerunning the same command should be safe.

## Technology stack

- Language: whatever fits the distribution target (Go for single-binary, Node.js + npm for JS ecosystem, Python for data tooling, Rust for performance)
- Argument parsing: a real arg-parser library (commander, click, cobra), not hand-rolled
- Testing: unit + integration; integration runs the actual binary in a test harness

## Profile notes

CLIs benefit from far less ceremony than business apps. The preset's defaults are designed for layered business apps; this profile drops most of that. The remaining rules (validation, logging, testing) still matter.
