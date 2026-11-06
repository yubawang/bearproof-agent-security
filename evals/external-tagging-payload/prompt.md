---
tags: [skill-creator]
max_turns: 30
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Write, Edit, Bash]
---

We want to auto-tag support tickets with an external LLM API (api.tagger.example). Each ticket row from our DB is a dict with: id, status, priority, product, created_at, customer_name, customer_email, customer_phone, assigned_agent, notes (free text our agents type; it often contains customer details). Write build_payload.py with build_payload(row: dict) -> dict that prepares one row to send without leaking private info, plus test_build_payload.py (pytest). Don't call the API.
