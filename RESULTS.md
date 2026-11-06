# Results: with and without the rules

**Short answer:** on these tasks, yes, the notes helped. Agents that read `RULES.md` first passed every check and avoided two real bugs that agents without it made both times.

## Method

- **Tasks:** 3 realistic coding prompts, in [`evals/`](evals/):
  1. `tailnet-approval-server`: a stdlib Python approval page exposed with `tailscale serve`.
  2. `external-tagging-payload`: build the payload that sends support tickets to an external LLM API without leaking private data.
  3. `remote-test-runner-script`: a CI script piped to `ssh host bash -s`, plus a check that a route is gone from a JSON config.
- **Runs:** each task done twice by fresh Claude Code subagents: once told to read `RULES.md` first, once with no rules. Same prompt otherwise.
- **Grading:** an independent grader agent saw the two outputs as "A" and "B" without knowing which had the rules. It graded 4 checks per task and also probed the code (running servers on loopback, sending raw requests, running scripts against stubs and edge-case fixtures) to find real bugs.
- **Two rounds:** round 1 used an earlier draft of the notes. Round 2 used the published `RULES.md` (v1) and stricter checks.

## Numbers

| Round | Checks passed (with rules) | Checks passed (without) | Avg time (with / without) | Avg tokens (with / without) |
|---|---|---|---|---|
| 1 (draft) | 12/12 | 9/12 | 309 s / 308 s | 93k / 90k |
| 2 (v1) | 12/12 | 8/12 | 323 s / 464 s | 94k / 106k |

Per task, round 2:

| Task | With rules | Without |
|---|---|---|
| tailnet-approval-server | 4/4 | 3/4 |
| external-tagging-payload | 4/4 | 2/4 |
| remote-test-runner-script | 4/4 | 3/4 |

## Real bugs the grader found

Without the rules, in both rounds:
- **Private data leak.** The payload sent the free-text `notes` field after regex scrubbing. Probes got third-party and non-Latin names through in both rounds, plus a street address and a spelled-out email in round 1. (Note 5: denylists fail open.)
- **False green CI.** When the piped remote script was cut short, the job exited 0 without running the tests. (Note 10: end-of-script marker.)

Without the rules, round 1 only:
- **Loose Host check.** The server accepted any `*.ts.net` Host instead of one exact name. (Note 1.)

With the rules:
- Round 1: the config check missed the route name when it was JSON-escaped and hidden behind a duplicate key. Note 7 was then updated to cover duplicate keys with `jq --stream`.
- Round 2: no bugs found by the probes.

## Limits

- **Small sample:** one run per task per configuration per round. Treat this as directional evidence, not a benchmark.
- **Targeted tasks:** the tasks were chosen to touch the notes (1, 2, 5, 7, 9, 10). They show the notes change behavior where they apply, not that they help on unrelated work.
- **Author-written checks:** the rules author wrote the checks. In round 2, half of them passed for both runs, so the real bugs above are the stronger signal.
- **Cost varies:** time and tokens were similar in round 1 and lower with the rules in round 2. With one run each, that difference may be noise.
