---
name: langgraph-dockerfile
description: >
  This skill should be used when the user asks to "generate a Dockerfile for
  langgraph", "customize the langgraph container", "write a Dockerfile for my
  graph", or wants to inspect/modify the base image LangGraph uses.
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  cli: langgraph
  command: dockerfile
---

# langgraph-dockerfile

Generate a customizable Dockerfile for the user's LangGraph app, so they can
add system packages, secrets handling, or other layers before building.

## Step 1 — Install-check (Branch B)

Follow **`skills/_shared/install-check.md`**, **Branch B**, and run that protocol
**verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

**Canonical source:** Run **`uv run langgraph dockerfile --help`** (or
**`langgraph dockerfile --help`** when not using **`uv run`**) and treat that
output as the source of truth for this installation: which flags exist, their
defaults, and short descriptions.

1. Run **`--help`** from the same environment you will use for
   **`langgraph dockerfile`** (project root, same **`uv`** / PATH).
2. Parse **`--help`** to choose flags. If the user did not specify a flag, do not ask —
   use the CLI defaults shown there. Only ask if they explicitly request customization.

**If `--help` fails** (CLI missing, bad PATH, **`uv run`** errors, or the command exits
non-zero): follow **`skills/langchain-docs-search/SKILL.md`** to query **Docs by LangChain**
for **LangGraph CLI** and **`langgraph dockerfile`** (match Python vs
JavaScript docs to the user’s project when relevant).

**Docs vs installed CLI:** hosted docs may lag the installed **`langgraph`** version. When
**`--help` works**, prefer it over documentation for flags and defaults.

## Step 3 — Resolve Dockerfile path

If the user did not specify, default to **`./Dockerfile`** in the project root.
Ask only if a `Dockerfile` (or chosen path) already exists and would be overwritten.

## Step 4 — Run (create file)

From **project root**, run the CLI so the Dockerfile **exists on disk** :

```bash
uv run langgraph dockerfile [resolved flags]
```



## Step 5 — Show the generated file and suggest next step

Use **`Read`** to display the final Dockerfile after generation (and after any edits you make).
Highlight where the user can safely add custom **`RUN`** layers (typically before the final **`CMD`**).

For questions about supported Python versions or base images, follow **`skills/langchain-docs-search/SKILL.md`**.
