---
name: langsmith-threads
description: >
  This skill should be used when the user asks to "list threads", "show
  conversation threads in langsmith", "find a thread by id", or wants to
  inspect multi-turn agent conversations grouped by thread_id.
metadata:
  version: "0.1.0"
  cli: langsmith
  command: thread list
---

# langsmith-threads

List LangSmith conversation threads (multi-turn runs grouped by `thread_id`).

## Step 1 — Verify the LangSmith CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Confirm `LANGSMITH_API_KEY` is set.

## Step 3 — Resolve options (defaults if unspecified)

| Option       | Default     | Override flag             |
| ------------ | ----------- | ------------------------- |
| Project      | most recent | `--project <name>`        |
| Limit        | 20          | `--limit <int>`           |
| Format       | JSON        | `--format pretty`         |
| Min turns    | unset       | `--min-turns <int>`       |

## Step 4 — Run

```bash
langsmith thread list [flags]
```

## Step 5 — Render

Table — thread_id, turn_count, first_seen, last_seen, total_tokens.

If the user wants to drill into a single thread's runs, hand off to
`langsmith-traces` and pass the relevant `thread_id` as a metadata filter:

```bash
langsmith trace list --project <name> --metadata thread_id=<id>
```

For thread modeling questions, defer to `langchain-docs-search`.
