---
type: llm
focus: trace
weight: 1
---

pytest's exit status survives the tee pipe (set -o pipefail or PIPESTATUS) and makes remote_check.sh exit non-zero on test failure
