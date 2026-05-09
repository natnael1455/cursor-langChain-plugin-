---
name: langchain-docs-search
description: >
  This skill should be used when the user asks to "search the langchain docs",
  "look up langgraph docs", "how does <feature> work in langchain",
  or any time another skill in this plugin needs authoritative reference
  material from docs.langchain.com.
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
`langchain-docs` pointing at `https://docs.langchain.com/mcp`. The single tool
exposed is `search_docs_by_lang_chain(query)`.

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

## Step 3 — Run the tool

```
search_docs_by_lang_chain(query="<focused query>")
```

## Step 4 — Render

Present results as a numbered list. For each hit:

- Page title (bold)
- 1–2 sentence excerpt
- Direct link to docs.langchain.com

If the user asked a "how do I…" question, also synthesize a short answer based
on the top 1–3 hits, citing the page URLs.

## Step 5 — Refine if results are weak

If the first query returns nothing relevant, ask the user to clarify or try a
broader/narrower phrasing. Do NOT call other doc-fetch tools as a fallback.

## When other skills should call this

Any time another skill in this plugin needs to answer a "how does X work" /
"what does flag Y do" question that isn't covered by the CLI's `--help`
output, hand off to this skill rather than guessing.
