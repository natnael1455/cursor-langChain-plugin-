---
name: langsmith-runs
description: >
  This skill should be used when the user asks to "list LLM calls", "list runs",
  "find tool calls", "export runs to JSONL", "show me chain runs in <project>",
  or wants to inspect / export individual runs (not full traces).
metadata:
  version: "0.1.0"
  cli: langsmith
  command: run list/export
---

# langsmith-runs

List or export LangSmith runs (individual LLM / tool / chain calls).

## Step 1 — Verify the LangSmith CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Same as other LangSmith skills — confirm `LANGSMITH_API_KEY` is set.

## Step 3 — Choose subcommand

**`run list`** — list runs (default).
**`run export <file.jsonl>`** — write runs to JSONL for offline analysis or
fine-tuning.

## Step 4 — Resolve options (defaults if unspecified)

| Option           | Default     | Override flag                |
| ---------------- | ----------- | ---------------------------- |
| Project          | most recent | `--project <name>`           |
| Run type         | all         | `--run-type llm|tool|chain`  |
| Name filter      | unset       | `--name <name>`              |
| Include metadata | off         | `--include-metadata`         |
| Full payloads    | off         | `--full`                     |
| Limit            | 50          | `--limit <int>`              |
| Format           | JSON        | `--format pretty`            |

If the user said "LLM calls", set `--run-type llm` automatically; "tool calls"
→ `--run-type tool`; "chain runs" → `--run-type chain`.

## Step 5 — Run

```bash
langsmith run list [flags]
# or
langsmith run export <file.jsonl> [flags]
```

## Step 6 — Render / report

For `list`: table — id, run_type, name, latency_ms, total_tokens (if available),
status.

For `export`: confirm file path, line count (`wc -l <file>`), and total size.

Defer model-specific or evaluation-related questions to `langchain-docs-search`.
