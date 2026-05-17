---
description: Run the LangGraph dev server (in-memory, hot-reload). Pairs with the langgraph-dev skill.
argument-hint: [--port N] [--host HOST]
---

Follow **`skills/langgraph-dev/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

Merge **`$ARGUMENTS`** into resolved CLI flags only where the user passed them — after **`--help`**. Do **not** ask for flags they did not specify; use CLI defaults from **`help`**.

## Step 1 — Install-check (Branch B)

- Run **`skills/_shared/install-check.md` Branch B verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

- **Canonical:** **`uv run langgraph dev --help`** from project root (same env as execution).
- If **`--help` lists `--no-browser`**, include **`--no-browser`** by default unless the user clearly asked the CLI to auto-open browsers.
- If **`--help` fails**, hand off lookup to **`skills/langchain-docs-search/SKILL.md`** (LangGraph CLI / **`langgraph dev`**); when **`--help` succeeds**, prefer it over docs.

## Open LangGraph Studio (default: OS browser)

- API base (e.g. **`http://127.0.0.1:2024`**) is not Studio; construct **`https://smith.langchain.com/studio/?baseUrl=...`** matching **`--host` / `--port`**.
- Default: open Studio in the **OS default browser** after the server listens (or print the URL if no shell **`open`** is acceptable).

## Step 3 — Run — long-lived server (Cursor)

- **`cd`** to project root, then **`uv run langgraph dev [resolved flags including $ARGUMENTS when provided]`**.
- **Preferred:** Integrated Terminal with the pasteable **`cd` + `uv run`** lines; leave running until Ctrl+C / user stop.
- **Detach (agent):** one **`nohup … >> .cursor/langgraph-dev.log 2>&1 & echo $!`** pattern per the skill; verify from log/banner — treat instant PTY exit as harness failure, not graph failure.

## Step 4 — Surface common issues

- Port conflicts → **`--port`** / next free port; instant unknown exit → retry Integrated Terminal or **`nohup`** path from the skill.
- Deeper product questions → **`skills/langchain-docs-search/SKILL.md`**.
