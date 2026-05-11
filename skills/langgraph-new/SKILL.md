---
name: langgraph-new
description: >
  This skill should be used when the user asks to "create a new langgraph
  project", "scaffold a langgraph app", "start a new graph from a template",
  "langgraph new", or wants to bootstrap an agent project from one of the
  official templates. In Cursor Plan mode: ask for template first. In Agent mode:
  scaffold, uv sync, copy .env from .env.example when present, default template
  blank (or minimal id from --help).
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  cli: langgraph
  command: new
---

# langgraph-new

Scaffold a new LangGraph project from an official template.

## Plan mode vs Agent mode (Cursor)

**Plan mode** (planning, before any shell commands that create files):

1. **Template:** Require an explicit template choice. Run `langgraph new --help` (read-only is fine) and present the **template ids** exactly as the user’s installed CLI lists them. Do not rely only on a hardcoded list; template ids change between CLI versions.
2. **Plan output** must record: project path/name, chosen template id, ordered post-scaffold steps, **`uv sync`** in the project root when `pyproject.toml` exists, and that **`.env` will be created from `.env.example`** when that file exists after scaffold and `.env` is not already present (say “copy if present” if the template is not yet known).

**Agent mode** (execution):

- Honor prior plan answers when the user already chose a template.
- If template was not chosen: infer the best match from the user’s description using ids from `langgraph new --help`. When still ambiguous, default to **`blank`** if the help lists it. If **`blank`** is not listed, use the **most minimal** template id from the help output—for example **`new-langgraph-project-python`** when you need a bare Python starter and that id appears (always confirm against `--help`; do not assume ids that are not shown).
- **Dependencies:** After scaffold, **`cd` into the project**. If **`pyproject.toml`** exists, run **`uv sync`** (do not create or activate a venv manually). If the template has no `pyproject.toml`, install with **`uv pip install -e .`** from the project root when `uv` is available, else **`pip install -e .`** once.
- **`.env`:** If **`.env.example`** is present in the project root and **`.env` does not exist**, copy it: Unix/macOS: `cp .env.example .env`; Windows (PowerShell): `Copy-Item .env.example .env`. If the template uses another sample file (e.g. `env.example`), use what the repo actually contains; default convention is `.env.example`. Do not overwrite an existing `.env`. Tell the user to fill in real secrets and keys in `.env`.

## Step 1 — Install-check (Branch A)

Follow **`skills/_shared/install-check.md`**, **Branch A** (`langgraph-new` only):
global **`uv`** and **`langgraph`** with **`command -v langgraph && langgraph --version`** — never silently install; matches **`rules/langchain-toolkit-core.mdc`** Hard rule 1.

## Step 2 — Pick a target directory

Default: a new directory with the project name the user gave (or
`my-langgraph-app` if they didn’t). Ask only if the name or parent path is ambiguous.

## Step 3 — Resolve template ids

Always prefer **`langgraph new --help`** for the authoritative template list on this machine.

Use:

```bash
langgraph new <path> --template <template-id>
```

`<template-id>` must match the CLI’s help output exactly.

## Step 4 — Scaffold

Run the `langgraph new` command with the chosen path and template. Then apply
**`uv sync`** (or the install fallback above) and **`.env.example` → `.env`** per
the Agent mode rules.

## Step 5 — Show next steps

After scaffolding and **`uv sync`** (or equivalent):

1. `cd <path>`.
2. If not already run: **`uv sync`** when `pyproject.toml` exists.
3. If `.env` was created from `.env.example`, remind the user to edit `.env` with real values; mention any API keys and tracing-related variables the template documents.
4. Use the **`langgraph-dev`** skill to start the dev server (it will use **`uv run langgraph dev`** when applicable).

For template-specific design questions, defer to **`skills/langchain-docs-search/SKILL.md`**.
