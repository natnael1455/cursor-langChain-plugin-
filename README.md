# langchain-toolkit (Cursor plugin)

Cursor plugin that wraps the **LangGraph** CLI as on-demand skills, ships an
always-on **rule** that injects the install-check protocol into Composer/Agent
context, and registers the official **Docs by LangChain** MCP server for
documentation lookup.

## What's inside

```
cursor-plugin/
├── .cursor-plugin/plugin.json        # plugin manifest
├── mcp.json                          # langchain-docs MCP server
├── rules/
│   └── langchain-toolkit-core.mdc    # always-applied install-check & routing rule
├── skills/
│   ├── _shared/install-check.md      # shared install-check protocol
│   ├── langgraph-{dev,up,build,dockerfile,new,deploy}/SKILL.md
│   └── langchain-docs-search/SKILL.md
├── commands/
│   ├── lg-dev.md                     # /lg-dev   shortcut for langgraph-dev
│   ├── lg-up.md                      # /lg-up
│   ├── lg-build.md                   # /lg-build
│   ├── lg-deploy.md                  # /lg-deploy
│   └── docs.md                       # /docs   → langchain-docs-search
└── README.md
```

## Components

- **MCP server** — `langchain-docs` (HTTP, `https://docs.langchain.com/mcp`).
- **Always-on rule** — `rules/langchain-toolkit-core.mdc` keeps the
  install-check protocol in Composer's persistent context so it's enforced
  even when a skill isn't explicitly invoked.
- **Skills** — LangGraph-focused `SKILL.md` files plus the docs search skill.
- **Slash commands** — short aliases for the most common skills.

## Install-check contract

Every LangGraph CLI skill begins with **`skills/_shared/install-check.md`**:

- **`langgraph-new`** → **Branch A** (global **`uv`** + **`langgraph`** before scaffold).
- All other LangGraph CLI skills → **Branch B** (both **`pyproject.toml`** and
  **`langgraph.json`**; dev **`langgraph-cli[inmem]`** + **`uv sync`** as specified;
  then **`uv run langgraph …`**).

Detect **`langgraph`** with **`command -v langgraph && langgraph --version`** (never
probe **`langgraph-cli`** on PATH). Offer **pip** / **`uv tool install`** / in-project
deps per that file — **ask before installing** (matches **`rules/langchain-toolkit-core.mdc`**).

## Setup

1. **Install in Cursor** — copy this folder into your `~/.cursor/plugins/` or
   submit it through the Cursor Marketplace. See
   [Cursor docs → plugins](https://cursor.com/docs/reference/plugins).
2. The `langchain-docs` MCP server is registered automatically via
   `mcp.json`. After install, open Cursor → Settings → MCP and verify it shows
   green.
3. For **`langgraph up`**, **`langgraph deploy`**, and tracing-heavy flows, set
   **`LANGSMITH_API_KEY`** (and **`LANGSMITH_ENDPOINT`** if your workspace needs it)
   as required by your project and the CLI.

## Usage

Either invoke skills naturally in Composer ("spin up langgraph dev",
"deploy this graph") or use the slash commands:

| Command       | Skill                  |
| ------------- | ---------------------- |
| `/lg-dev`     | `langgraph-dev`        |
| `/lg-up`      | `langgraph-up`         |
| `/lg-build`   | `langgraph-build`      |
| `/lg-deploy`  | `langgraph-deploy`     |
| `/docs`       | `langchain-docs-search`|
