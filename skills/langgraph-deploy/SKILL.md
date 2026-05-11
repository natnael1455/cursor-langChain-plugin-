---
name: langgraph-deploy
description: >
  This skill should be used when the user asks to "deploy my langgraph app",
  "deploy to LangGraph hosted deployment", "ship my agent", "langgraph deploy", or
  needs to push a graph to LangChain's hosted deployment platform.
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  cli: langgraph
  command: deploy
---

# langgraph-deploy

Deploy a LangGraph application via the LangGraph **`deploy`** CLI (hosted deployment).

## Step 1 — Install-check (Branch B)

Follow **`skills/_shared/install-check.md`**, **Branch B**, and run that protocol
**verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

**Canonical source:** Run **`uv run langgraph deploy --help`** (or
**`langgraph deploy --help`** when not using **`uv run`**) and treat that
output as the source of truth for this installation: which flags exist, their
defaults, and short descriptions.

1. Run **`--help`** from the same environment you will use for
   **`langgraph deploy`** (project root, same **`uv`** / PATH).
2. Parse **`--help`** to choose flags. If the user did not specify a flag, do not ask —
   use the CLI defaults shown there. Only ask if they explicitly request customization.

**If `--help` fails** (CLI missing, bad PATH, **`uv run`** errors, or the command exits
non-zero): follow **`skills/langchain-docs-search/SKILL.md`** to query **Docs by LangChain**
for **LangGraph CLI** and **`langgraph deploy`** (match Python vs
JavaScript docs to the user’s project when relevant).

**Docs vs installed CLI:** hosted docs may lag the installed **`langgraph`** version. When
**`--help` works**, prefer it over documentation for flags and defaults.

## Step 3 — Verify deployment credentials

Check **`printenv LANGSMITH_API_KEY | head -c 6`**. If empty, ask the user to set
it before continuing — deploys typically fail without it. If **`langgraph deploy --help`**
or current docs list other required environment variables, verify presence the same way.
Confirm their workspace plan and deployment access match what the CLI expects.

## Step 4 — Project layout

Branch B **`install-check`** already requires **`langgraph.json`** and **`pyproject.toml`**
at **project root**. If you are not in that layout, delegate to **`langgraph-new`**
before **`langgraph deploy`**.

## Step 5 — Run

From **project root** (after Branch B **`install-check`**, including **`uv sync`** when
needed):

```bash
uv run langgraph deploy [resolved flags]
```

Stream the build/deploy logs back to the user.

## Step 6 — Show outcome and suggest next step

After success, surface the deployment URL (or identifier) printed by the CLI. Recommend they
confirm in the deployment UI that the new revision is serving, then hit the endpoint and verify
behavior (logs, health checks, or tracing as their setup provides).

For platform-specific questions (custom domains, autoscaling, secrets), defer to
**`skills/langchain-docs-search/SKILL.md`**.
