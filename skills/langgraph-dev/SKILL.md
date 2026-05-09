---
name: langgraph-dev
description: >
  This skill should be used when the user asks to "run the langgraph dev server",
  "start the dev server", "spin up langgraph locally", "test my graph", "run
  langgraph in development mode", or wants in-memory hot-reload local execution
  of a LangGraph app without Docker. Prefer uv sync and uv run langgraph dev
  in project directories with pyproject.toml. Start langgraph dev in a
  background terminal (Shell tool)—never blocking foreground babysitting—use
  --no-browser by default when listed in --help, open LangSmith Studio in the OS
  default browser, and keep the server until the user asks to stop or closes
  the Cursor project/workspace.
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  cli: langgraph
  command: dev
---

# langgraph-dev

Run the LangGraph API server in development mode (in-memory, hot-reload, no Docker).

## Step 1 — Install-check (Branch B)

Follow **`skills/_shared/install-check.md`**, **Branch B**, and run that protocol
**verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

**Canonical source:** Run **`uv run langgraph dev --help`** (or **`langgraph dev --help`**
when not using **`uv run`**) and treat that output as the source of truth for this
installation: which flags exist, their defaults, and short descriptions.

1. Run **`--help`** from the same environment you will use for **`langgraph dev`** (project
   root, same **`uv`** / PATH).
2. Parse **`--help`** to choose flags. If the user did not specify a flag, do not ask —
   use the CLI defaults shown there. Only ask if they explicitly request customization.

**Agent default (merge with `--help`, do not skip `--help`):** If **`langgraph dev --help`**
lists **`--no-browser`**, include **`--no-browser`** on every **`uv run langgraph dev`**
invocation by default—so **`langgraph dev`** does not spawn browsers in agent/headless
contexts. **Exception:** Omit **`--no-browser`** only when the user clearly asks for
**`langgraph dev` itself** to auto-open whatever the CLI would open. **Do not conflate** that with
LangSmith Studio: Studio uses **[Open LangGraph Studio (default: OS browser)](#open-langgraph-studio-default-os-browser)**
below unless the user explicitly wants otherwise.

**If `--help` fails** (CLI missing, bad PATH, **`uv run`** errors, or the command exits
non-zero): fall back to **LangChain documentation** via the MCP server
**`plugin-langchain-toolkit-langchain-docs`**. If tool names are unclear, inspect that
server’s tool descriptor JSON under the project **`mcps/plugin-langchain-toolkit-langchain-docs`**
folder, then search the docs for **LangGraph CLI** and **`langgraph dev`** (match Python vs
JavaScript docs to the user’s project when relevant).

**Docs vs installed CLI:** hosted docs may lag the installed **`langgraph`** version. When
**`--help` works**, prefer it over documentation for flags and defaults.

## Open LangGraph Studio (default: OS browser)

**`http://127.0.0.1:2024`** (or whatever **`--host`** / **`--port`** you resolved) is the
**Agent Server API**, not LangGraph Studio. LangSmith hosts Studio with **`baseUrl`** pointing
at your local server ([LangGraph local server / Studio](https://docs.langchain.com/oss/python/langgraph/local-server)):

Construct:

`https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024`

(match **`baseUrl`** to **`--host`** / **`--port`** and scheme if you ever diverge).

**Default for agents/users:** After **`langgraph dev`** is listening, open that Studio URL in
the **system default OS browser** when a shell command is acceptable—for example **`open '<url>'`** (macOS),
**`xdg-open '<url>'`** (Linux), or **`start "" '<url>'`** (Windows). If invoking a helper is not
acceptable, print the full URL clearly and instruct the user to paste it into the default browser.

With **`--no-browser`**, the CLI prints the API URL and Studio link in logs; reuse that link or rebuild it from **`--host`** / **`--port`**.

**Optional (not default):** **Simple Browser** or **Integrated Browser** in Cursor if the user
prefers editing in-editor—not the primary workflow for this skill.

## Step 3 — Run — long-lived server (Cursor)

From **project root**, with **`[resolved flags]`** from Step 2 (**`install-check`** already ran **`uv sync`**
via Branch B when needed). **`cd`** there first so **`uv`** and **`langgraph.json`** resolve correctly.

### Why a second attempt is usually not “two servers”

If the **first** try ends in **~100–200 ms** with **`exit_code: unknown`**, **almost no log output**, and **no banner**
(API / Studio URLs, “started”), that almost always means the **agent Shell harness or pseudo-terminal** shuts down **before** **`langgraph dev`** could run normally—not that LangGraph is broken. Retrying via **Integrated Terminal** or **`nohup` + log** below is swapping **launch method**, not intentionally running **duplicate** servers. **Do not** tell the user they have two stacks from that symptom alone.

### Preferred (most reliable): Integrated Terminal

1. Ask the user to open **Terminal → New Terminal** (Integrated Terminal).
2. Print the exact **`cd`** + **`uv run`** lines so they can paste and run—or run them there yourself if acting as user in that tab.

```bash
cd "<project-root>"
uv run langgraph dev [resolved flags]
```

Leave that tab open; **Ctrl+C** stops the server. This matches a normal developer workflow and avoids fragile agent-only background PTYs.

### If the agent must start the server without holding a foreground Shell forever

**Rule:** Still **never** tie up one Shell invocation for the **whole** lifetime of **`langgraph dev`** (blocking babysit).

**Anti-pattern:** A bare **`uv run langgraph dev`** launched as an **immediate-return “background task”**
(e.g. non-blocking Cursor Shell with **`block_until_ms`** / **`0`** and **no detach**) commonly tears the PTY **down instantly**: **no startup banner**, **unknown exit**. That is **not** evidence **`langgraph dev`** fails on the project—**do not** blame LangSmith or **`langgraph.json`** solely from this.

**Acceptable detach pattern:** a **single** shell command that **detaches** **`langgraph dev`** and **persists logs** (same **`[resolved flags]`**):

```bash
mkdir -p .cursor && nohup uv run langgraph dev [resolved flags] >> .cursor/langgraph-dev.log 2>&1 & echo $!
```

- Prefer **`.cursor/langgraph-dev.log`** (or **`tmp/`**); remind the user **`git`** may need that path **`gitignore`d**.
- **`nohup`** reduces **SIGHUP** when the runner drops the controlling terminal.
- **Save the printed PID**; use it only for this session’s teardown (**`kill <pid>`**), never broad **`pkill`**.

Then **verify** from the log—not from an empty ephemeral terminal transcript.

### Startup verification

After **Integrated Terminal** or **`nohup`** start:

1. Brief wait (seconds), then **read/`tail`** integrated stdout or **`.cursor/langgraph-dev.log`** until you see the usual banner (**API URL**, **Studio**/LangSmith **`baseUrl`**, healthy “listening” semantics per CLI output)—or conclude the **launch method failed** and pivot to Integrated Terminal (**Preferred** above).
2. Treat **instant exit + no banner + unknown code** from an agent Shell job as **harness/start-path failure**, retry with **Integrated Terminal** or **`nohup` + log** before assuming project misconfiguration.

Then [**open Studio in the default OS browser**](#open-langgraph-studio-default-os-browser)—or print the **`baseUrl`** link if skipping open.

**Do not**

- Stop the server merely because **one agent turn** finished.

**Process lifetime**

- **Stop** **`langgraph dev`** only when (**a**) the **user explicitly asks** (e.g. “stop the dev server”), or (**b**) **teardown for this Cursor project/workspace** / **chat session for this project** applies. Tear down safely: **Ctrl+C** in the Integrated Terminal tab that owns the process **or** **`kill <pid>`** for the **`nohup`** child you recorded—or stop the matching **background job**. Never **`kill`** unrelated processes.

## Step 4 — Surface common issues

- **Port already in use** → suggest `--port 2025` (or next free port).
- **Instant Shell exit (~100 ms), unknown exit code, no startup banner** → almost always **runner/PTY teardown**. Retry **Integrated Terminal** or **`nohup` + `.cursor/langgraph-dev.log`** (Step 3) before debugging LangGraph or the project graph.

For deeper questions about graph definitions, streaming, checkpoints, etc., use
the `langchain-docs-search` skill to query the Docs by LangChain MCP.
