---
name: langgraph-new
description: >
  This skill should be used when the user asks to "create a new langgraph
  project", "scaffold a langgraph app", "start a new graph from a template",
  "langgraph new", or wants to bootstrap an agent project from one of the
  official templates.
metadata:
  version: "0.1.0"
  cli: langgraph
  command: new
---

# langgraph-new

Scaffold a new LangGraph project from an official template.

## Step 1 — Verify the LangGraph CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U "langgraph-cli[inmem]"

## Step 2 — Pick a target directory

Default: a new sibling directory with the project name the user gave (or
`my-langgraph-app` if they didn't). Ask only if a name is ambiguous.

## Step 3 — Pick a template

Run `langgraph new --help` to list current templates. Common ones:

- `react-agent` — single-graph ReAct-style agent
- `memory-agent` — long-term memory example
- `retrieval-agent` — RAG-style retrieval agent

If the user did not pick one, default to `react-agent` and tell them which
template you used.

## Step 4 — Run

```bash
langgraph new <path> --template <template>
```

## Step 5 — Show next steps

After scaffolding, surface:

1. `cd <path> && pip install -e .`
2. Set `LANGSMITH_API_KEY` and any model API keys the template needs.
3. Use the `langgraph-dev` skill to start the dev server.

For template-specific design questions, defer to `langchain-docs-search`.
