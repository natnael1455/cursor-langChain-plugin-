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

Follow **`skills/_shared/install-check.md`**, **Branch B**.

## Step 2 — Verify deployment credentials

Check `printenv LANGSMITH_API_KEY | head -c 6`. If empty, ask the user to set
it before continuing — deploys typically fail without it. Confirm their workspace
plan and deployment access match what the CLI expects.

## Step 3 — Project layout

Branch B **`install-check`** already requires **`langgraph.json`** and **`pyproject.toml`**
at **project root**. If you are not in that layout, delegate to **`langgraph-new`**
before **`langgraph deploy`**.

## Step 4 — Resolve options (defaults if unspecified)

| Option        | Default                                | Override flag      |
| ------------- | -------------------------------------- | ------------------ |
| Deployment    | first deployment in workspace          | `--deployment <name>` |
| Config        | `langgraph.json`                       | `-c <path>`        |
| Wait          | true                                   | `--no-wait`        |
| Tag           | git short SHA                          | `-t <tag>`         |

Use defaults unless the user specifies otherwise.

## Step 5 — Run

From **project root** (after Branch B **`install-check`**):

```bash
uv run langgraph deploy [resolved flags]
```

Stream the build/deploy logs back to the user. After success, surface the
deployment URL printed by the CLI.

## Step 6 — Suggest verification

Recommend they confirm in the deployment UI that the new revision is serving,
then hit the endpoint and verify behavior (logs, health checks, or tracing as
their setup provides).

For platform-specific questions (custom domains, autoscaling, secrets), defer
to `langchain-docs-search`.
