---
description: Search official LangChain and LangGraph documentation via the langchain-docs MCP server (Docs by LangChain).
argument-hint: <search query>
---

Follow **`skills/langchain-docs-search/SKILL.md`** verbatim for full protocol. Use the checkpoints below.

If **`$ARGUMENTS`** is non-empty, use it as (or trim it into) the focused query in Step 2. If empty, ask the user what they want to look up.

## Step 1 — Verify the MCP server is connected

- This flow **skips** install-check (no CLI). Confirm the **`langchain-docs`** MCP is available and tools are callable.
- Prefer **`search_docs_by_lang_chain(query=...)`**; optional **`query_docs_filesystem_docs_by_lang_chain(command=...)`** for **`rg`** / **`cat`** / **`head`** on the virtual docs tree — same server.
- Do **not** fall back to arbitrary web scraping; if the MCP is missing, give the disconnected-server message from the skill and stop.

## Step 2 — Form the query

- Strip fluff; keep symbols and concepts verbatim (classes, APIs, LangGraph phrases).
- If **`$ARGUMENTS`** narrows intent, incorporate it exactly.

## Step 3 — Run the tool(s)

- Run semantic search first; use the filesystem reader when keyword/API path pinning is needed (see **`Python import paths`** in the skill).

## Step 4 — Render

- Numbered hits: bold title, short excerpt, direct **`docs.langchain.com`** link.
- For “how do I” questions, add a tight synthesis citing top hits.

## Python import paths (`langchain` vs `langchain_core`)

- Ground in **`pyproject.toml`** pins + MCP results — follow **`skills/langchain-docs-search/SKILL.md`** subsection in full.

## Step 5 — Refine if results are weak

- Rephrase, clarify with the user, or retry **`query_docs_filesystem_docs_by_lang_chain`** per the skill. No undocumented fetchers outside this MCP pair.

## When other skills should call this

- Defer CLI behavior you cannot read from **`--help`** to this skill/doc workflow; detailed routing is in **`skills/langchain-docs-search/SKILL.md`**.
