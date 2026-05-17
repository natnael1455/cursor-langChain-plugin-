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

Follow **`skills/_shared/install-check.md`**, **Branch B**, and run that protocol
**verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

**Canonical source:** Run **`uv run langgraph up --help`** (or
**`langgraph up --help`** when not using **`uv run`**) and treat that
output as the source of truth for this installation: which flags exist, their
defaults, and short descriptions.

1. Run **`--help`** from the same environment you will use for
   **`langgraph up`** (project root, same **`uv`** / PATH).
2. Parse **`--help`** to choose flags. If the user did not specify a flag, do not ask —
   use the CLI defaults shown there. Only ask if they explicitly request customization.

**If `--help` fails** (CLI missing, bad PATH, **`uv run`** errors, or the command exits
non-zero): follow **`skills/langchain-docs-search/SKILL.md`** to query **Docs by LangChain**
for **LangGraph CLI** and **`langgraph up`** (match Python vs
JavaScript docs to the user’s project when relevant).

**Docs vs installed CLI:** hosted docs may lag the installed **`langgraph`** version. When
**`--help` works**, prefer it over documentation for flags and defaults.

## Step 3 — Verify Docker is running

Run **`docker info`** non-interactively. If it fails, tell the user Docker Desktop
(or the Docker engine they use) must be running before **`langgraph up`** can run, and stop here.

## Step 4 — Verify required credentials

**`langgraph up`** typically expects **`LANGSMITH_API_KEY`** in the environment.
Run **`printenv LANGSMITH_API_KEY | head -c 6`** to check presence (not value). If
empty, ask the user to set it before continuing. If **`langgraph up --help`** or
current docs mention additional required variables, apply the same presence check
pattern for those.

## Step 5 — Run — long-lived stack (Cursor)

From **project root**, with **`[resolved flags]`** from Step 2 (**`install-check`** already ran **`uv sync`**
via Branch B when needed). **`cd`** there first so **`uv`** and **`langgraph.json`** resolve correctly.

**Preferred for interactive use:** **Integrated Terminal** — print the exact **`cd`** + **`uv run`** lines:

```bash
cd "<project-root>"
uv run langgraph up [resolved flags]
```

Tail logs in that tab until the user interrupts (**Ctrl+C** stops the stack per Compose behavior).

**If the agent must not hold a foreground Shell forever:** use the same **detach** idea as
**`langgraph-dev`** — a **single** command that backgrounds the process and **persists logs**, e.g.
**`nohup uv run langgraph up [resolved flags] >> .cursor/langgraph-up.log 2>&1 & echo $!`**, then verify
from the log file. Remind the user log paths may need **`gitignore`**.

## Step 6 — Stop

When the user asks to stop, from **project root** (or the compose file location your run used), run
whatever **`langgraph up --help`** documents for teardown. The historical default is often:

```bash
docker compose -f .langgraph_api/docker-compose.yml down
```

Confirm the path still matches the CLI / generated layout for this project if that fails.

## Step 7 — Surface common issues

- **Docker not reachable** → Step 3; start the engine before retrying.
- **Missing API key** → Step 4; set **`LANGSMITH_API_KEY`** (and any other vars **`--help`** lists).
- **Port in use** → pick another port if **`langgraph up --help`** exposes one (e.g. **`-p`**), or free the port.

For graph design, streaming, or hosted deployment details, follow **`skills/langchain-docs-search/SKILL.md`**
or **`skills/langgraph-deploy/SKILL.md`**.
