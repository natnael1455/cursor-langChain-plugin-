---
name: langsmith-evaluators
description: >
  This skill should be used when the user asks to "list evaluators", "upload an
  evaluator", "delete an evaluator", "register an offline evaluator in
  langsmith", or wants to manage evaluator definitions.
metadata:
  version: "0.1.0"
  cli: langsmith
  command: evaluator list/upload/delete
---

# langsmith-evaluators

Manage LangSmith evaluators (list / upload / delete).

## Step 1 — Verify the LangSmith CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U langsmith-cli

## Step 2 — Verify API key

Confirm `LANGSMITH_API_KEY` is set.

## Step 3 — Choose subcommand

**`evaluator list`** — show all evaluators in the workspace.
**`evaluator upload <file.py> --name <n> --function <fn> --dataset <name>`** —
register an offline (Python) evaluator and bind it to a dataset.
**`evaluator delete <name> --yes`** — delete an evaluator (irreversible).

## Step 4 — Per-subcommand defaults

### `list`
| Option | Default | Override |
| ------ | ------- | -------- |
| Limit  | 50      | `--limit <int>` |
| Format | JSON    | `--format pretty` |

### `upload`
Required: file path, `--name`, `--function`, `--dataset`. Validate the file
exists and the function is defined before invoking. If the user didn't
specify a name, derive one from the function name (kebab-case).

### `delete`
Always require explicit `--yes` and confirm with the user verbally first
("Are you sure you want to delete evaluator `<name>`? This cannot be undone.").

## Step 5 — Run

```bash
langsmith evaluator list [flags]
langsmith evaluator upload <file.py> --name <n> --function <fn> --dataset <ds>
langsmith evaluator delete <name> --yes
```

## Step 6 — Render

For `list`: table — name, type (offline/online), dataset, last_run_at.
For `upload`: confirm the new evaluator name and the dataset binding.
For `delete`: confirm what was removed.

For evaluator authoring questions (signature, return value, scoring
conventions), defer to `langchain-docs-search`.
