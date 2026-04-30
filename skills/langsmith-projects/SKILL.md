---
name: langsmith-projects
description: >
  This skill should be used when the user asks to "list langsmith projects",
  "show my tracing projects", "what projects exist in langsmith", or wants to
  see the tracing project namespace before drilling into traces or runs.
metadata:
  version: "0.1.0"
  cli: langsmith
  command: project list
---

# langsmith-projects

List LangSmith tracing projects.

## Step 1 — Verify the LangSmith CLI is installed

Follow `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Check `printenv LANGSMITH_API_KEY | head -c 6`. If empty, ask the user to set
it before continuing.

## Step 3 — Resolve options (defaults if unspecified)

| Option       | Default | Override flag        |
| ------------ | ------- | -------------------- |
| Limit        | 20      | `--limit <int>`      |
| Format       | JSON    | `--format pretty`    |
| Workspace    | default | `--workspace <id>`   |

## Step 4 — Run

```bash
langsmith project list [resolved flags]
```

## Step 5 — Render

If `--format pretty` was used, pass output through to the user verbatim.
Otherwise parse the JSON and present a compact table with: name, ID,
last_run_at, run_count.

If the user wants to drill into a project, hand off to `langsmith-traces` or
`langsmith-runs`.
