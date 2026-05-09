# Install-check protocol

This protocol **implements Hard rule 1** of **`rules/langchain-toolkit-core.mdc`**
(install-check before **`langgraph`**; ask before installing; never silently
install; offer **`pip`**, **`uv tool install`**, or in-project
**`langgraph-cli[inmem]`**).

Every CLI-backed LangGraph skill **must** run the correct **branch** below before
invoking **`langgraph`**. Reference this file from `SKILL.md` and follow the steps
verbatim.

## Naming: detect vs install

- **PATH detection** uses only the **`langgraph`** executable:
  **`command -v langgraph && langgraph --version`** (global) or
  **`uv run langgraph --version`** (inside the project after sync). **Never**
  probe **`langgraph-cli`** on PATH—that string names the PyPI distribution, not a
  guaranteed binary ([LangGraph CLI](https://docs.langchain.com/langsmith/cli.md)).
- **`langgraph-cli[inmem]`** is the package string for **`pip`**,
  **`uv tool install`**, and **`pyproject.toml`** dev dependencies.

## Routing

| Invoking skill | Branch |
| -------------- | ------ |
| **`langgraph-new`** | **Branch A** |
| **`langgraph-dev`**, **`langgraph-up`**, **`langgraph-build`**, **`langgraph-deploy`**, **`langgraph-dockerfile`**, and other LangGraph CLI skills | **Branch B** |

---

## Branch A — `langgraph-new` only

Use before **`langgraph new`** when there may be **no** project yet.

1. **Detect `uv`:** `command -v uv` (record version).
2. **Detect `langgraph`:** **`command -v langgraph && langgraph --version`**. Do
   **not** use **`langgraph-cli`** on PATH.
3. If **`uv`** or **`langgraph`** is missing: explain the gap, **ask for
   confirmation**, then install — **never silently install**. For **`langgraph`**,
   offer **`pip install -U "langgraph-cli[inmem]"`** or
   **`uv tool install "langgraph-cli[inmem]"`** per the core rule. For **`uv`**,
   install only after explicit user confirmation (installer or package manager per
   user preference).
4. After any install, **re-run** the detect commands from steps 1–2.

---

## Branch B — all other LangGraph CLI skills

Use when operating against an existing LangGraph app layout.

1. **Detect `uv`:** `command -v uv`. If missing: explain, **ask for
   confirmation**, install **`uv`** only if confirmed — otherwise **stop**. Do
   **not** silently install **`uv`**.
2. **Project markers** at **project root** (directory containing
   **`langgraph.json`**, or the parent directory of the file passed to **`-c`**
   when the skill resolves a config path):
   - Require **`pyproject.toml`** **and** **`langgraph.json`** both present.
   - If **either** is missing: **stop**. Tell the user the layout is incomplete and
     **hand off to the `langgraph-new` skill** — do **not** run **`langgraph`**
     commands on Branch B.
3. **Dev dependency and sync** (both markers present):
   - Read **`pyproject.toml`** and locate dev dependencies, in order:
     - PEP 735 **`[dependency-groups]`** → group **`dev`**,
     - else **`[project.optional-dependencies]`** → extra **`dev`**,
     - else legacy **`[tool.uv.dev-dependencies]`** if present.
   - If **`langgraph-cli[inmem]`** is **not** listed in that dev surface: explain,
     **ask for confirmation**, then edit **`pyproject.toml`** with a minimal diff —
     **never** silently edit. Only after confirmation: add **`langgraph-cli[inmem]`**
     to the correct section, then **`uv sync`**:
     - **`uv sync --group dev`** when using **`[dependency-groups]`**,
     - **`uv sync --extra dev`** when using **`optional-dependencies`**,
     - **`uv sync`** when only **`tool.uv.dev-dependencies`** applies (typical
       **`uv`** behavior).
   - **Never** run **`uv sync`** after unsolicited **`pyproject.toml`** edits.
   - Verify in the project env: **`uv run langgraph --version`** (recommended after
     sync).
4. Invoke **`langgraph`** only as **`uv run langgraph …`** from project root.

---

## Detect table (reference)

| Tool | Detect command | Install (only after user confirms) |
| ---- | -------------- | ----------------------------------- |
| **`uv`** | `command -v uv && uv --version` | Per user preference after confirmation |
| **`langgraph`** | `command -v langgraph && langgraph --version` | `pip install -U "langgraph-cli[inmem]"` or `uv tool install "langgraph-cli[inmem]"` |

Use the terminal tool for detect commands. Capture stdout and exit code.

If the user **declines** install or **`pyproject.toml`** edits, **stop** and explain
that the task cannot proceed.

After running an install command, **re-run** the relevant detect command. If it
still fails, surface the install output and **stop** — do not retry blindly.

---

## Implementation notes

- Prefer **`uv sync`** + **`uv run …`** inside LangGraph projects instead of manual
  venvs.
- For global LangGraph installs when **`uv`** is available, prefer
  **`uv tool install "langgraph-cli[inmem]"`** when the user agrees.
- Prefer **`pip install --user`** on system Pythons where global writes may be
  denied.
- On macOS with Homebrew Python, **`pip install --break-system-packages`** may be
  needed only if plain **`pip install`** fails with
  **`externally-managed-environment`**.
- Never invoke **`sudo`** automatically — surface the error and let the user decide.
