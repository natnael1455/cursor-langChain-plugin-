---
description: Generate a Dockerfile for LangGraph customization. Pairs with the langgraph-dockerfile skill.
argument-hint: [dockerfile-output-path-or-flags]
---

Follow **`skills/langgraph-dockerfile/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

Merge **`$ARGUMENTS`** after **`uv run langgraph dockerfile --help`**. Do **not** ask for unspecified flags; default Dockerfile path **`./Dockerfile`** per skill unless **`$ARGUMENTS`**/`help` dictates otherwise.

## Step 1 — Install-check (Branch B)

- Run **`skills/_shared/install-check.md` Branch B verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

- **Canonical:** **`uv run langgraph dockerfile --help`** from project root.
- **`--help` failure**→ **`skills/langchain-docs-search/SKILL.md`** for **`langgraph dockerfile`**; when **`help` succeeds**, prefer **`help`**.

## Step 3 — Resolve Dockerfile path

- Default **`./Dockerfile`**; confirm before overwrite.

## Step 4 — Run (create file)

```bash
uv run langgraph dockerfile [resolved flags including $ARGUMENTS when provided]
```

## Step 5 — Show the generated file and suggest next step

- **`Read`** the Dockerfile for the user; note safe insertion points (**`RUN`** before final **`CMD`**). Base/Python questions → **`skills/langchain-docs-search/SKILL.md`**.
