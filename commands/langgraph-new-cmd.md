---
description: Scaffold a new LangGraph app from official templates. Pairs with the langgraph-new skill.
argument-hint: [path-or-name] [--template TEMPLATE_ID]
---

Follow **`skills/langgraph-new/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

Use **`$ARGUMENTS`** for path/name and **`--template`** when provided; unknown pieces follow **`langgraph new --help`** ids — never invent template ids absent from **`help`**.

## Plan mode vs Agent mode (Cursor)

- **Plan:** require explicit template from **`langgraph new --help`**; record target path + template + **`uv sync`** + `.env.example`→`.env` handling.
- **Agent:** scaffold, template default rules (**`blank`** or minimal id from **`help`**) — see skill.

## Step 1 — Install-check (Branch A)

- Run **`skills/_shared/install-check.md` Branch A verbatim** (global **`uv` / langgraph`; never silently install).

## Step 2 — Pick a target directory

- Default new directory name from user/`$ARGUMENTS`; **`my-langgraph-app`** only if unnamed; clarify only ambiguous paths/names.

## Step 3 — Resolve template ids

- Authoritative list: **`langgraph new --help`** on this machine; **`langgraph new <path> --template <id>`** with exact id strings.

## Step 4 — Scaffold

- Execute **`langgraph new`** then **`uv sync`** (and **`.env.example` → `.env`** copy rules) exactly as in the skill’s Agent mode rules.

## Step 5 — Show next steps

- **`cd`** path; **`uv sync`** if pending; **`langgraph-dev`** skill for **`uv run langgraph dev`**, etc. Template questions: **`skills/langchain-docs-search/SKILL.md`**.
