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

Follow **`skills/_shared/install-check.md`**, **Branch B**.

## Step 2 — Resolve target path (default: `Dockerfile`)

If the user did not specify, default to `./Dockerfile` in the project root. Ask
only if a `Dockerfile` already exists and would be overwritten.

## Step 3 — Run

From **project root** (after Branch B **`install-check`**, including **`uv sync`** when
needed):

```bash
uv run langgraph dockerfile <path> [-c langgraph.json]
```

## Step 4 — Show the generated file

Use `Read` to display the new Dockerfile. Highlight where the user can safely
add custom `RUN` layers (typically before the final `CMD`).

## Step 5 — Suggest next step

Recommend `langgraph-build` to build from the customized Dockerfile. For
questions about supported Python versions or base images, use
`langchain-docs-search`.
