# langchain-toolkit (Cursor plugin)

Cursor plugin that wraps the **LangGraph** and **LangSmith** CLIs as on-demand
skills, ships an always-on **rule** that injects the install-check protocol
into Composer/Agent context, and registers the official **Docs by LangChain**
MCP server for documentation lookup.

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
│   ├── langsmith-{projects,traces,runs,datasets,experiments,evaluators,threads}/SKILL.md
│   └── langchain-docs-search/SKILL.md
├── commands/
│   ├── lg-dev.md                     # /lg-dev   shortcut for langgraph-dev
│   ├── lg-up.md                      # /lg-up
│   ├── lg-build.md                   # /lg-build
│   ├── lg-deploy.md                  # /lg-deploy
│   ├── ls-traces.md                  # /ls-traces
│   ├── ls-runs.md                    # /ls-runs
│   └── docs.md                       # /docs   → langchain-docs-search
└── README.md
```

## Components

- **MCP server** — `langchain-docs` (HTTP, `https://docs.langchain.com/mcp`).
- **Always-on rule** — `rules/langchain-toolkit-core.mdc` keeps the
  install-check protocol in Composer's persistent context so it's enforced
  even when a skill isn't explicitly invoked.
- **Skills** — 14 SKILL.md files covering every LangGraph and LangSmith CLI
  command group, plus the docs search skill.
- **Slash commands** — short aliases for the most common skills.

## Install-check contract

Every CLI-backed skill begins with the install-check protocol from
`skills/_shared/install-check.md`:

1. Detect whether `langgraph` / `langsmith` is on PATH.
2. If missing, surface the exact `pip install` command and confirm with the
   user before running it.
3. Only proceed once the CLI is verified.

## Setup

1. **Install in Cursor** — copy this folder into your `~/.cursor/plugins/` or
   submit it through the Cursor Marketplace. See
   [Cursor docs → plugins](https://cursor.com/docs/reference/plugins).
2. The `langchain-docs` MCP server is registered automatically via
   `mcp.json`. After install, open Cursor → Settings → MCP and verify it shows
   green.
3. Set `LANGSMITH_API_KEY` in your shell environment for any LangSmith skill.
4. For `langgraph deploy`, ensure your LangSmith workspace has Deployments
   enabled.

## Usage

Either invoke skills naturally in Composer ("spin up langgraph dev",
"list recent traces") or use the slash commands:

| Command       | Skill                  |
| ------------- | ---------------------- |
| `/lg-dev`     | `langgraph-dev`        |
| `/lg-up`      | `langgraph-up`         |
| `/lg-build`   | `langgraph-build`      |
| `/lg-deploy`  | `langgraph-deploy`     |
| `/ls-traces`  | `langsmith-traces`     |
| `/ls-runs`    | `langsmith-runs`       |
| `/docs`       | `langchain-docs-search`|
