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
│   └── langchain-toolkit-core.mdc    # always-applied install-check & routing
├── skills/
│   ├── _shared/install-check.md      # shared install-check protocol (Branches A/B)
│   ├── langgraph-{dev,up,build,dockerfile,new,deploy}/SKILL.md
│   └── langchain-docs-search/SKILL.md
├── commands/
│   ├── lg-dev.md                     # /lg-dev   → langgraph-dev
│   ├── lg-up.md                      # /lg-up    → langgraph-up
│   ├── lg-build.md                   # /lg-build → langgraph-build
│   ├── lg-deploy.md                  # /lg-deploy → langgraph-deploy
│   └── docs.md                       # /docs     → langchain-docs-search
└── README.md
```

`langgraph-new` and `langgraph-dockerfile` are available as skills only (no dedicated slash
command in this manifest); invoke them from Composer or by name.

## Components

- **MCP server** — **`langchain-docs`** (HTTP, `https://docs.langchain.com/mcp`), wired via `mcp.json`.
- **Always-on rule** — `rules/langchain-toolkit-core.mdc` keeps the install-check
  protocol in Composer’s persistent context so it is enforced even when a skill is
  not explicitly invoked.
- **Skills** — LangGraph-focused `SKILL.md` files plus **`langchain-docs-search`**. Highlights:
  - **`langgraph-dev`** — **`langgraph dev`** with **`--help`**-driven flags and Branch B **`uv run`**.
  - **`langgraph-dockerfile`** — **`langgraph dockerfile`**, **`--help`**-driven flags, then a **mandatory post-create edit** of the generated Dockerfile when it lives under a subdirectory: rewrite local-package **`ADD`** sources to a **POSIX relpath** from the Dockerfile’s directory to the **`langgraph.json`** project root, and fix **JSON-array `ADD` / quoted `WORKDIR`** when **`/deps/...`** paths contain spaces.
  - **`langgraph-new`** — Branch **A** install-check (before **`langgraph new`**).
- **Slash commands** — Short aliases for the skills listed in **commands/** (see table below).

## Install-check contract

Every LangGraph CLI skill starts from **`skills/_shared/install-check.md`**:

- **`langgraph-new`** → **Branch A** (global **`uv`** + **`langgraph`** before scaffold).
- All other LangGraph CLI skills → **Branch B** (both **`pyproject.toml`** and
  **`langgraph.json`**; dev **`langgraph-cli[inmem]`** + **`uv sync`** as specified;
  then **`uv run langgraph …`**).

Detect **`langgraph`** with **`command -v langgraph && langgraph --version`** (never
probe **`langgraph-cli`** on PATH). Offer **pip** / **`uv tool install`** / in-project
deps per that file — **ask before installing** (matches **`rules/langchain-toolkit-core.mdc`**).

## Setup

1. **Install in Cursor** — Install from the Marketplace, or copy this tree into a folder under
   **`~/.cursor/plugins/`** (for example **`~/.cursor/plugins/local/langchain-toolkit`** for a
   local checkout). See [Cursor docs → plugins](https://cursor.com/docs/reference/plugins).
2. **`langchain-docs`** is registered via **`mcp.json`**. After install, open Cursor →
   Settings → MCP and confirm the server is healthy.
3. For **`langgraph up`**, **`langgraph deploy`**, and tracing-heavy flows, set
   **`LANGSMITH_API_KEY`** (and **`LANGSMITH_ENDPOINT`** if your workspace needs it)
   as required by your project and the CLI.

### Local development

To iterate from a git clone, copy or sync the plugin directory into
**`~/.cursor/plugins/local/langchain-toolkit`**, then reload Cursor. Example:

```bash
rsync -av --exclude '.git' --exclude '.gitignore' ./cursor-plugin/ ~/.cursor/plugins/local/langchain-toolkit/
```

(Adjust the source path to your clone.)

## Usage

Either invoke skills in Composer ("spin up langgraph dev", "generate a langgraph Dockerfile")
or use the slash commands below.

| Command      | Skill                |
| ------------ | -------------------- |
| `/lg-dev`    | `langgraph-dev`      |
| `/lg-up`     | `langgraph-up`       |
| `/lg-build`  | `langgraph-build`    |
| `/lg-deploy` | `langgraph-deploy`   |
| `/docs`      | `langchain-docs-search` |

Other skills (**`langgraph-new`**, **`langgraph-dockerfile`**) are invoked by natural language or by name; see **`skills/*/SKILL.md`**.
