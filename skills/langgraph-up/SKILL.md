---
name: langgraph-up
description: >
  This skill should be used when the user asks to "run langgraph with docker",
  "start the full langgraph stack", "langgraph up", "run langgraph with postgres
  and redis", or needs the production-equivalent local API server (Postgres +
  Redis + LangGraph API) via Docker Compose.
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  cli: langgraph
  command: up
---

# langgraph-up

Start the full LangGraph API server stack locally via Docker Compose
(LangGraph API + Postgres + Redis).

## Step 1 — Install-check (Branch B)

Follow **`skills/_shared/install-check.md`**, **Branch B**.

## Step 2 — Verify Docker is running

Run `docker info` non-interactively. If it fails, tell the user Docker Desktop
must be started before `langgraph up` can run, and stop here.

## Step 3 — Verify required credentials

`langgraph up` expects `LANGSMITH_API_KEY` in the environment. Run
`printenv LANGSMITH_API_KEY | head -c 6` to check presence (not value). If
empty, ask the user to set it before continuing.

## Step 4 — Resolve options (defaults if unspecified)

| Option            | Default | Override flag                |
| ----------------- | ------- | ---------------------------- |
| Port              | `8123`  | `-p <port>`                  |
| Wait for ready    | off     | `--wait`                     |
| Watch & restart   | off     | `--watch`                    |
| Verbose logs      | off     | `--verbose`                  |
| Config path       | `langgraph.json` | `-c <path>`         |
| Extra compose     | none    | `-d <docker-compose.yml>`    |

## Step 5 — Run

From **project root** (after Branch B **`install-check`**, including **`uv sync`** when
needed):

```bash
uv run langgraph up [resolved flags]
```

Tail logs until the user interrupts, or detach if running in CI.

## Step 6 — Stop

When the user asks to stop, run:

```bash
docker compose -f .langgraph_api/docker-compose.yml down
```

For graph design questions, defer to `langchain-docs-search`.
