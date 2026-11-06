---
type: llm
focus: trace
weight: 1
---

approve_server.py deliberately avoids leftover-body smuggling: it sets protocol_version to HTTP/1.0 explicitly, or every refusal path for a POST reads the body or closes the connection
