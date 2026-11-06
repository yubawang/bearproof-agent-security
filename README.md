# Bearproof Agent Security 🐻🔒

Security field notes for coding agents. The aim is simple: what the agent builds should not open with a lucky paw swipe.

Twelve short, concrete lessons from running coding agents and the small local services they build: localhost apps behind a tailnet proxy, Python `http.server`, web-framework middleware, and shell scripts that verify their own work. Each note says what was seen, why it happens, the rule, and how to test for it.

## Contents

- [`RULES.md`](RULES.md): the 12 notes.
- [`RESULTS.md`](RESULTS.md): does it help? Agents with and without the notes, on the same tasks, graded blind.
- [`evals/`](evals/): the 3 test tasks and their pass/fail checks.

## Who it is for

Anyone who lets a coding agent (Claude Code, Codex, or any tool that reads `AGENTS.md`) build, expose or verify services.

## How to use

- Paste `RULES.md` into your `CLAUDE.md` or `AGENTS.md`, or
- keep it in the repo and add one line: "Before exposing a service or claiming a check passed, follow `RULES.md`."

Versions and dates are given where they matter. Re-test on your own versions; behavior changes.

## Results in one line

On 3 security-sensitive coding tasks, agents with the notes passed 12/12 checks against 8/12 without, and avoided two real bugs the baseline made: leaking customer details from a free-text field, and a CI script that reported success without running tests. Details and limits in [`RESULTS.md`](RESULTS.md).

## License

MIT. See [`LICENSE`](LICENSE).
