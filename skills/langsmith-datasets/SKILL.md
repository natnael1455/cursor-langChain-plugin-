---
name: langsmith-datasets
description: >
  This skill should be used when the user asks to "list langsmith datasets",
  "show eval datasets", "what datasets do I have", or wants to discover the
  evaluation dataset namespace before running experiments.
metadata:
  version: "0.1.0"
  cli: langsmith
  command: dataset list
---

# langsmith-datasets

List LangSmith evaluation datasets.

## Step 1 — Verify the LangSmith CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Confirm `LANGSMITH_API_KEY` is set.

## Step 3 — Resolve options (defaults if unspecified)

| Option       | Default | Override flag         |
| ------------ | ------- | --------------------- |
| Limit        | 50      | `--limit <int>`       |
| Format       | JSON    | `--format pretty`     |
| Search       | unset   | `--search <substr>`   |

## Step 4 — Run

```bash
langsmith dataset list [flags]
```

## Step 5 — Render

Present a compact table — name, id, example_count, created_at,
last_modified_at.

If the user wants to see experiments run against a dataset, hand off to
`langsmith-experiments`. For dataset construction docs, defer to
`langchain-docs-search`.
