---
name: langchain-docs-search
description: >
  This skill should be used when the user asks to "search the langchain docs",
  "look up langgraph docs", or "how does <feature> work in langchain"; when
  resolving ambiguous Python imports (`langchain` vs `langchain_core`); or any
  time another skill in this plugin needs authoritative reference material from
  docs.langchain.com.
metadata:
  version: "0.1.0"
  description: "LangGraph CLI skills with auto install-check, plus the Docs by LangChain MCP for in-editor doc lookup."
  mcp_server: langchain-docs
---

# langchain-docs-search

Query the official **Docs by LangChain** MCP server for documentation snippets
covering LangChain, LangGraph, and related ecosystem topics in Docs by LangChain.

## Step 1 — Verify the MCP server is connected

This skill does NOT depend on a local CLI, so it skips the install-check
protocol. Instead, verify the MCP server is reachable.

The `.mcp.json` at the plugin root registers an HTTP MCP server named
`langchain-docs` pointing at `https://docs.langchain.com/mcp`. That server exposes:

- **`search_docs_by_lang_chain(query)`** — semantic search across Docs by LangChain.
- **`query_docs_filesystem_docs_by_lang_chain(command)`** — read-only `rg`, `cat`, `head`,
  etc. against a virtual tree of `.mdx` doc pages on the MCP host (same server). Use after
  search when you need an exact keyword hit or a known API-reference path.

For **`langchain_core` vs `langchain` import ambiguity**, prefer the dedicated workflow below.

If the tool is not visible in the current session (the MCP server failed to
connect), tell the user:

> The `langchain-docs` MCP server isn't connected in this session. Reload the
> plugin or restart Claude Code, then try again.

Do not attempt to fall back to web fetching — the MCP path is preferred for
provenance and freshness.

## Step 2 — Form the query

Reduce the user's question to a focused search query:

- Strip generic words ("how do I", "is there a way to") — keep proper nouns
  and concepts ("streaming tokens from a langgraph node").
- If the user mentioned a specific class/function (e.g. `StateGraph`,
  `RunnableLambda`, `evaluate`), include it verbatim.

## Step 3 — Run the tool(s)

Semantic search:

```
search_docs_by_lang_chain(query="<focused query>")
```

Optional filesystem read (same MCP server; stateless per call; output truncated — prefer
targeted `rg -C` or `head -N`):

```
query_docs_filesystem_docs_by_lang_chain(command="<e.g. rg -il \"Symbol\" /api-reference/>")
```

## Step 4 — Render

Present results as a numbered list. For each hit:

- Page title (bold)
- 1–2 sentence excerpt
- Direct link to docs.langchain.com

If the user asked a "how do I…" question, also synthesize a short answer based
on the top 1–3 hits, citing the page URLs.

## Python import paths (`langchain` vs `langchain_core`)

Editors may suggest **`langchain_core.*`** paths that disagree with façade imports or with
what the user's environment actually exposes. Ground answers in **`pyproject.toml`**
pins and **Docs by LangChain** MCP results — not editor quick-fixes or training-data
module paths alone.

1. **Read `pyproject.toml`** (project root). Note versions for **`langchain`**, **`langchain-core`**, and any other **`langchain-*`** packages that might own the symbol. Check **`[tool.uv.sources]`** when present.
2. **`search_docs_by_lang_chain`**: include the **symbol verbatim** (e.g. `HumanMessage`, `ChatOpenAI`) and words like **Python import** / **API reference**; when helpful, add version text from `pyproject.toml` inside the query string (the tool has no separate version parameter).
3. **Render** as in Step 4. When the docs show a clear **`from … import …`**, surface that as the **recommended import line** and keep the **`docs.langchain.com`** URL from the hit. **Both** `from langchain_core…` and `from langchain…` re-exports can appear depending on the page; **`pyproject.toml`** tells you which wheels are installed — MCP tells you the documented module path for the API surface.
4. **If still fuzzy**: call **`query_docs_filesystem_docs_by_lang_chain`** with **`rg`** (or similar) for the symbol under **`/api-reference/`** (or broader `/` if needed). When quoting a path to the user, convert virtual paths to URLs by stripping `.mdx` (e.g. `/path/page.mdx` → `https://docs.langchain.com/path/page`).

**Limitation:** Live docs skew toward what the site publishes today. If pinned versions are
very old or prereleases, say so — suggest aligning dependencies if imports or APIs may differ.

## Step 5 — Refine if results are weak

If the first **`search_docs_by_lang_chain`** query returns nothing relevant, ask the user to clarify or try a broader/narrower phrasing — **or**, for locating a specific docs page path or keyword, retry with **`query_docs_filesystem_docs_by_lang_chain`** on the **`langchain-docs`** MCP server (see Step 3 and **Python import paths** above).

Do **not** fall back to arbitrary web scraping or undocumented fetchers outside this MCP pair.

## When other skills should call this

Any time another skill in this plugin needs to answer a "how does X work" /
"what does flag Y do" question that isn't covered by the CLI's `--help`
output, hand off to this skill rather than guessing.

Same for resolving **ambiguous LangChain Python imports** or module paths (**`rules/langchain-toolkit-core.mdc`** rule 4 — use this skill instead of guessed editor imports).
