# Install-check protocol

Every CLI-backed skill in this plugin **must** run this protocol before doing
anything else. Reference it from `SKILL.md` and follow the steps verbatim.

## Step 1 — Detect

Run a non-interactive check that exits 0 only if the CLI is on PATH:

| CLI       | Detect command                              | Install command                         |
| --------- | ------------------------------------------- | --------------------------------------- |
| LangGraph | `command -v langgraph && langgraph --version` | `pip install -U "langgraph-cli[inmem]"` |
| LangSmith | `command -v langsmith && langsmith --version` | `pip install -U langsmith-cli`          |

Use the terminal tool to execute the detect command. Capture both stdout and
the exit code.

## Step 2 — Branch on result

**If exit code is 0** (CLI present): record the version in your reasoning and
proceed to the skill's main task.

**If exit code is non-zero** (CLI missing): do NOT silently install. Tell the
user clearly and ask for confirmation:

> The `<cli>` CLI is not installed on this system. I can install it with:
>
>     <install command>
>
> Should I run this now? (yes / no)

If the user confirms, run the install command. If they decline, stop the skill
and let them know the task can't proceed without the CLI.

## Step 3 — Re-verify after install

After running the install command, run the detect command again. If it still
fails, surface the install output and stop — do not retry blindly.

## Implementation notes

- Always prefer `pip install --user` if the user is on a system Python where
  global writes might be denied.
- On macOS where Homebrew Python is in use, `pip install --break-system-packages`
  may be needed. Detect this only if the plain `pip install` fails with
  `externally-managed-environment`.
- Never invoke `sudo` automatically — surface the error and let the user decide.
