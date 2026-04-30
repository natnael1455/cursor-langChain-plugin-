---
name: langsmith-experiments
description: >
  This skill should be used when the user asks to "list experiments", "show
  experiment results", "what experiments ran on <dataset>", or wants to
  compare evaluation runs over a LangSmith dataset.
metadata:
  version: "0.1.0"
  cli: langsmith
  command: experiment list
---

# langsmith-experiments

List LangSmith experiments (evaluation runs against a dataset).

## Step 1 — Verify the LangSmith CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Confirm `LANGSMITH_API_KEY` is set.

## Step 3 — Determine dataset

Experiments are scoped to a dataset. If the user didn't specify, run
`langsmith dataset list --limit 5 --format pretty` and ask which to use, OR
fall back to the most recently modified dataset.

## Step 4 — Resolve options (defaults if unspecified)

| Option       | Default | Override flag             |
| ------------ | ------- | ------------------------- |
| Dataset      | (req.)  | `--dataset <name>`        |
| Limit        | 20      | `--limit <int>`           |
| Format       | JSON    | `--format pretty`         |
| Sort by      | newest  | `--sort latency|score`    |

## Step 5 — Run

```bash
langsmith experiment list --dataset <name> [flags]
```

## Step 6 — Render

Table — experiment_name, run_count, mean_score, mean_latency_ms, created_at.
Highlight the best-scoring experiment.

Hand off to `langsmith-evaluators` if the user wants to know which evaluators
contributed to scores. For evaluation methodology questions, defer to
`langchain-docs-search`.
