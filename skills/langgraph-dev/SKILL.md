---
name: langgraph-dev
description: >
  This skill should be used when the user asks to "run the langgraph dev server",
  "start the dev server", "spin up langgraph locally", "test my graph", "run
  langgraph in development mode", or wants in-memory hot-reload local execution
  of a LangGraph app without Docker.
metadata:
  version: "0.1.0"
  cli: langgraph
  command: dev
---

# langgraph-dev

Run the LangGraph API server in development mode (in-memory, hot-reload, no Docker).

## Step 1 — Verify the LangGraph CLI is installed

Follow the protocol in `skills/_shared/install-check.md`. Detect with
`command -v langgraph && langgraph --version`. If missing, offer:

    pip install -U "langgraph-cli[inmem]"

Do not proceed past this step until detection succeeds.

## Step 2 — Confirm the working directory has a langgraph.json

Run `ls langgraph.json` in the user's project root. If absent, ask the user
whether to:

- run `langgraph new` first (delegate to the `langgraph-new` skill), or
- point at a different directory with `-c <path>`.

## Step 3 — Resolve options (defaults if unspecified)

| Option         | Default     | Override flag      |
| -------------- | ----------- | ------------------ |
| Host           | `127.0.0.1` | `--host <host>`    |
| Port           | `2024`      | `--port <int>`     |
| Auto-reload    | on          | `--no-reload`      |
| Open browser   | on          | `--no-browser`     |
| Debug port     | unset       | `--debug-port <n>` |
| Config path    | `langgraph.json` | `-c <path>`   |

If the user did not specify a flag, do not ask — use the default. Only ask if
they explicitly request customization.

## Step 4 — Run

```bash
langgraph dev [resolved flags]
```

Stream output back to the user. The dev server typically opens
`http://127.0.0.1:2024` and the LangGraph Studio UI in a browser tab.

## Step 5 — Surface common issues

- **Port already in use** → suggest `--port 2025` (or next free port).
- **`langgraph.json` not found** → see Step 2.
- **Auth errors against LangSmith** → remind user to set `LANGSMITH_API_KEY`.

For deeper questions about graph definitions, streaming, checkpoints, etc., use
the `langchain-docs-search` skill to query the Docs by LangChain MCP.
