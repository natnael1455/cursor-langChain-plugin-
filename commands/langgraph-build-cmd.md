---
description: Build a LangGraph Docker image. Pairs with the langgraph-build skill.
argument-hint: [-t TAG] [--platform PLATFORMS]
---

Follow **`skills/langgraph-build/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

Merge **`$ARGUMENTS`** into resolved CLI flags after **`langgraph build --help`**. Do **not** ask for unspecified flags; use defaults from **`help`**.

## Step 1 — Install-check (Branch B)

- Run **`skills/_shared/install-check.md` Branch B verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

- **Canonical:** **`uv run langgraph build --help`** from project root (same env as **`uv run langgraph build`**).
- Multi-arch asks (e.g. amd64 + arm64) merge with **`--help`** syntax when supported — still run **`help` first**.
- If **`--help` fails**, use **`skills/langchain-docs-search/SKILL.md`**; when **`--help` succeeds**, prefer it over docs.

## Step 3 — Verify Docker buildx is available

- **`docker buildx version`**; if missing, stop and advise Docker tooling with **buildx**.

## Step 4 — Run

```bash
uv run langgraph build [resolved flags including $ARGUMENTS when provided]
```

## Step 5 — Report result and suggest next step

- Surface image ID/tag from CLI; optional **`docker push`**, **`skills/langgraph-deploy/SKILL.md`**, or **`skills/langchain-docs-search/SKILL.md`** when relevant.
