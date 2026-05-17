---
description: Start the full LangGraph Docker Compose stack. Pairs with the langgraph-up skill.
argument-hint: [-p PORT] [--watch]
---

Follow **`skills/langgraph-up/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

Merge **`$ARGUMENTS`** after **`uv run langgraph up --help`**. Do **not** ask for unspecified flags.

## Step 1 — Install-check (Branch B)

- Run **`skills/_shared/install-check.md` Branch B verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

- **Canonical:** **`uv run langgraph up --help`** from project root (same env as **`uv run langgraph up`**).
- If **`--help` fails**, **`skills/langchain-docs-search/SKILL.md`**; when **`help` succeeds**, prefer **`help`** over docs.

## Step 3 — Verify Docker is running

- **`docker info`** non-interactively; if it fails, stop — start Docker/engine first.

## Step 4 — Verify required credentials

- Typically **`LANGSMITH_API_KEY`**: **`printenv LANGSMITH_API_KEY | head -c 6`**. Mirror the same presence pattern for variables **`langgraph up --help`** documents.

## Step 5 — Run — long-lived stack (Cursor)

- From project root:

```bash
cd "<project-root>"
uv run langgraph up [resolved flags including $ARGUMENTS when provided]
```

- **Preferred:** Integrated Terminal; **detach:** **`nohup`** + log file (e.g. **`.cursor/langgraph-up.log`**) per the skill, then **`tail`**/`Read` verification.

## Step 6 — Stop

- Tear down via what **`langgraph up --help`** documents; historically often **`docker compose -f .langgraph_api/docker-compose.yml down`** — confirm path matches this project.

## Step 7 — Surface common issues

- Docker down → Step 3; missing key → Step 4; port binding → alternate **`-p`** or free port per **`help`**.
