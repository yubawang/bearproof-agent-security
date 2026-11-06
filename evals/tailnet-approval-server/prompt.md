---
tags: [skill-creator]
max_turns: 30
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Write, Edit, Bash]
---

I've got a tiny approval page I want to reach from my phone. Write approve_server.py using only Python's standard library (http.server): GET / lists pending items from pending.json, POST /approve with a form field id moves that item into approved.json. It listens on 127.0.0.1:8765 and I expose it over my tailnet with `tailscale serve --bg 8765`. Only I (Tailscale login me@example.com) should be able to approve things. Keep it simple, and add a short NOTES.md explaining the security choices.
