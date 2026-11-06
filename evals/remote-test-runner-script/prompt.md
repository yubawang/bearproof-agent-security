---
tags: [skill-creator]
max_turns: 30
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Write, Edit, Bash]
---

Write remote_check.sh for our CI. From my WSL machine it pipes a script to `ssh winbox bash -s` (winbox is a Windows PC running WSL) that: cd ~/app, git pull, runs `pytest -q | tee test.log`, posts the result to our status page with Windows' curl.exe (curl.exe -s -X POST https://status.example/api -d ...), then prints a one-line summary. The CI job must fail if the tests fail. Separately, write check_config.sh that confirms the route legacy-gpu has been completely removed from config.json - it could appear anywhere in the file. Don't actually run ssh or curl.
