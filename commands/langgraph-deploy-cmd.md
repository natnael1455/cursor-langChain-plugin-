---
description: Deploy a LangGraph app to hosted LangGraph. Pairs with the langgraph-deploy skill.
argument-hint: [--deployment NAME] [-t TAG]
---

Follow **`skills/langgraph-deploy/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

Merge **`$ARGUMENTS`** after **`uv run langgraph deploy --help`**. Do **not** ask for unspecified flags.

## Step 1 — Install-check (Branch B)

- Run **`skills/_shared/install-check.md` Branch B verbatim** before any **`langgraph`** command.

## Step 2 — Resolve flags and defaults

- **Canonical:** **`uv run langgraph deploy --help`** from project root (same env as deploy).
- If **`--help` fails**, **`skills/langchain-docs-search/SKILL.md`** for LangGraph **`deploy`**; when **`help` succeeds**, prefer **`help`** over docs.

## Step 3 — Verify deployment credentials

- **`printenv LANGSMITH_API_KEY | head -c 6`** (presence only); extend to any vars **`deploy --help`** / current docs imply. Stop and ask user if required vars are absent.

## Step 4 — Run

```bash
uv run langgraph deploy [resolved flags including $ARGUMENTS when provided]
```

- Stream build/deploy logs to the user.

## Step 5 — Show outcome and suggest next step

- Surface deployment URL/identifier from stdout; confirm revision in UI; platform extras → **`skills/langchain-docs-search/SKILL.md`**.
