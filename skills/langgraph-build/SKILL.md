---
name: langgraph-build
description: >
  This skill should be used when the user asks to "build a langgraph docker
  image", "package my graph as a container", "build langgraph for deployment",
  or "build a multi-arch langgraph image".
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  cli: langgraph
  command: build
---

# langgraph-build

Build a Docker image of the user's LangGraph app for deployment.

## Step 1 — Install-check (Branch B)

Follow **`skills/_shared/install-check.md`**, **Branch B**, and run that protocol
**verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

**Canonical source:** Run **`uv run langgraph build --help`** (or
**`langgraph build --help`** when not using **`uv run`**) and treat that
output as the source of truth for this installation: which flags exist, their
defaults, and short descriptions.

1. Run **`--help`** from the same environment you will use for
   **`langgraph build`** (project root, same **`uv`** / PATH).
2. Parse **`--help`** to choose flags. If the user did not specify a flag, do not ask —
   use the CLI defaults shown there. Only ask if they explicitly request customization.

**If `--help` fails** (CLI missing, bad PATH, **`uv run`** errors, or the command exits
non-zero): follow **`skills/langchain-docs-search/SKILL.md`** to query **Docs by LangChain**
for **LangGraph CLI** and **`langgraph build`** (match Python vs
JavaScript docs to the user’s project when relevant).

**Docs vs installed CLI:** hosted docs may lag the installed **`langgraph`** version. When
**`--help` works**, prefer it over documentation for flags and defaults.

**Common explicit requests (merge with `--help`, do not skip `--help`):** If the user asks for
**multi-arch** (e.g. AMD64 + ARM64), include **`--platform linux/amd64,linux/arm64`** (or the exact
syntax **`langgraph build --help`** shows) when supported.

## Step 3 — Verify Docker buildx is available

Run **`docker buildx version`**. If missing, tell the user to update Docker Desktop (or install a
Docker setup that provides **buildx**) before **`langgraph build`**.

## Step 4 — Run

From **project root** (after Branch B **`install-check`**, including **`uv sync`** when
needed):

```bash
uv run langgraph build [resolved flags]
```

## Step 5 — Report result and suggest next step

Report the resulting image ID and tag from the CLI output. If the user mentioned a registry or
**`langgraph deploy`**, optionally suggest **`docker push <tag>`** or **`skills/langgraph-deploy/SKILL.md`**.

For deployment-target questions (hosted LangGraph, K8s, etc.), defer to **`skills/langchain-docs-search/SKILL.md`**
or **`skills/langgraph-deploy/SKILL.md`**.
