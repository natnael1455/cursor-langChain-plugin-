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

Follow **`skills/_shared/install-check.md`**, **Branch B**.

## Step 2 — Verify Docker buildx is available

Run `docker buildx version`. If missing, tell the user to update Docker Desktop.

## Step 3 — Resolve options (defaults if unspecified)

| Option       | Default                                  | Override flag        |
| ------------ | ---------------------------------------- | -------------------- |
| Tag          | `<plugin-name>:latest` from langgraph.json | `-t <tag>`         |
| Platform     | host arch                                | `--platform <list>`  |
| Pull base    | yes                                      | `--no-pull`          |
| Config path  | `langgraph.json`                         | `-c <path>`          |

If the user wants a multi-arch image for AMD64 + ARM64, use
`--platform linux/amd64,linux/arm64`.

## Step 4 — Run

From **project root** (after Branch B **`install-check`**):

```bash
uv run langgraph build [resolved flags]
```

Report the resulting image ID and tag back to the user. Optionally suggest
`docker push <tag>` if the user mentioned a registry.

For deployment-target questions (hosted LangGraph, K8s, etc.), defer to
`langchain-docs-search` or the `langgraph-deploy` skill.
