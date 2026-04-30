---
name: langsmith-traces
description: >
  This skill should be used when the user asks to "list traces", "find traces
  with errors", "get trace <id>", "show recent traces in langsmith", "debug a
  trace", or wants to inspect run trees in a tracing project.
metadata:
  version: "0.1.0"
  cli: langsmith
  command: trace list/get
---

# langsmith-traces

List and inspect LangSmith traces (full run trees).

## Step 1 — Verify the LangSmith CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Same as `langsmith-projects` — confirm `LANGSMITH_API_KEY` is set.

## Step 3 — Choose subcommand

**`trace list`** — list recent traces. Use when the user wants overview or
filtering.

**`trace get <id> [--full]`** — fetch a single trace. Use when the user
references a specific trace ID or URL.

If unclear, default to `trace list` for the user's most recent project.

## Step 4 — Resolve options for `trace list` (defaults if unspecified)

| Option       | Default     | Override flag             |
| ------------ | ----------- | ------------------------- |
| Project      | most recent | `--project <name>`        |
| Limit        | 20          | `--limit <int>`           |
| Errors only  | off         | `--errors`                |
| Min latency  | unset       | `--min-latency <ms>`      |
| Tag filter   | unset       | `--tag <tag>`             |
| Metadata     | unset       | `--metadata key=value`    |
| Format       | JSON        | `--format pretty`         |

## Step 5 — Run

```bash
langsmith trace list [flags]
# or
langsmith trace get <trace-id> --project <name> [--full]
```

## Step 6 — Render

For `list`: present a compact table — id, status, latency_ms, error,
start_time, name. Include direct links to smith.langchain.com when the CLI
returns them.

For `get`: summarize the run tree (root run → children) with timing per node
and surface any error stack traces.

For deeper questions about trace data shape or how runs are nested, defer to
`langchain-docs-search`.
