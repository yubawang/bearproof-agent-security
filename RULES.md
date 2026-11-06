# Security field notes for coding agents

Each note: **Seen** (version, date) · **Why** · **Rule** · **Test**.

## Exposure and auth

1. **Localhost apps behind Tailscale Serve**
   - Seen: Tailscale 1.102 (2026-11) keeps the client Host header and replaces any client-sent `Tailscale-User-Login` with the real one.
   - Why: a DNS-rebinding page can reach a localhost backend and add the login header itself.
   - Rule: bind to 127.0.0.1 (else peers forge headers); require Host to be exactly your node's `*.ts.net` name (plus port if not 443) AND an allowed login; on writes also require `Origin` to equal exactly `https://` + that Host (refuse if missing): a signed-in browser's cross-site request passes the first two. This stops browsers only: local processes can send any headers.
   - Test: an allowed login with `Host: evil.example`, and a write with a foreign or missing `Origin`, must be refused.

2. **Python `http.server` keep-alive**
   - Seen: CPython 3.13 with `protocol_version = "HTTP/1.1"` (2026-11).
   - Why: a handler that calls `send_response()` before reading the body leaves the body on the connection, parsed as the next request, so a cross-site POST can smuggle one with any headers. `send_error()` sends `Connection: close` and is safe.
   - Rule: use HTTP/1.0, or read the full body (or set `close_connection = True`) on every reply path.
   - Test: on every reply path, POST one connection whose body holds a second request; no second response may come.

3. **Middleware and login gates are not auth boundaries**
   - Seen: public framework middleware bypasses, e.g. Next.js CVE-2025-29927 (2025).
   - Why: middleware runs only on paths its matcher selects and can be skipped for some internal request types.
   - Rule: check auth where the data or key is used. Never let a proxy holding an API key rely on a UI login gate alone.
   - Test: without a session, call every forwarded route, its variants (trailing slash, encoded characters, added extensions, changed case) and known internal-header bypasses; each must refuse.

4. **Restart clients after adding auth**
   - Seen: a service kept keyless calls for 5 h after a patch (2026-11).
   - Why: running processes keep the code they loaded.
   - Rule: restart every client that imported the old code.
   - Test: each client's process start time is after the patch time.

5. **Denylists fail open**
   - Seen: a regex filter for private notes let through 9 of 10 private samples (names, phones, bare URLs, non-Latin text) (2026-11).
   - Why: a denylist misses every pattern nobody thought of.
   - Rule: send an external API only known-public fields (allowlist); drop free text by default.
   - Test: assert every payload's keys are a subset of the allowlist.

6. **Write endpoints in previews**
   - Seen: a preview ran the real tool once another session installed it (2026-11).
   - Why: a stub at start may be live now.
   - Rule: before a live test of a write endpoint, re-check what is installed; send only requests that must be refused.
   - Test: afterwards, the target's log shows only refused requests.

## Verification

7. **Text checks that cannot fail**
   - Seen: a config check passed on a string found in another block; an alias key kept a removed route alive (2026-11).
   - Why: file-wide "contains X" matches anywhere; `jq '.. | strings'` skips keys; most parsers keep only the last duplicate key.
   - Rule: parse the exact key; scan keys and shadowed duplicates with `jq --stream`.
   - Test: run the check on a copy saved before the change; it must fail.

8. **Clean-checkout test runs**
   - Seen: a suite passed locally, failed from a clean checkout (2026-11).
   - Why: fixtures sat in an ignored path, never committed.
   - Rule: run the suite from `git archive` of the commit.
   - Test: `git status --short --ignored` lists nothing the suite needs.

9. **Exit codes through pipes**
   - Seen: an aborted run looked like success (2026-11).
   - Why: `cmd | tail; echo $?` reports `tail`.
   - Rule: use `set -o pipefail` or `${PIPESTATUS[0]}`.
   - Test: with it set, `false | tail` reports non-zero.

10. **Scripts piped to `ssh host bash -s`**
    - Seen: later script lines silently never ran (2026-11).
    - Why: stdin readers eat lines meant for bash: `ssh` or a Windows exe via WSL interop eats the rest, `read` the next line.
    - Rule: give such commands `< /dev/null`.
    - Test: end the script with a marker line; check it printed.

11. **Who owns a port**
    - Seen: `pgrep -f` returned a wrapper shell, not the server (2026-11).
    - Why: it matches any process whose arguments contain the text.
    - Rule: take the pid from `ss -ltnp` (as root for other users' processes).
    - Test: `/proc/<pid>/cmdline` is the server itself.

12. **Scripts run through a symlink**
    - Seen: a symlinked script failed though its docs said it worked (2026-11).
    - Why: Python `__file__` keeps the symlink path, so sibling config is not found.
    - Rule: use `os.path.realpath(__file__)`.
    - Test: run the installed symlink itself.
