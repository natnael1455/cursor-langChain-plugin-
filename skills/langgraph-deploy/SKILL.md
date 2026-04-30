---
name: langgraph-deploy
description: >
  This skill should be used when the user asks to "deploy my langgraph app",
  "deploy to LangSmith Deployment", "ship my agent", "langgraph deploy", or
  needs to push a graph to LangChain's hosted deployment platform.
metadata:
  version: "0.1.0"
  cli: langgraph
  command: deploy
---

# langgraph-deploy

Deploy a LangGraph application to LangSmith Deployment via the LangGraph
deploy CLI.

## Step 1 — Verify the LangGraph CLI is installed

See `skills/_shared/install-check.md`. Install if needed:

    pip install -U "langgraph-cli[inmem]"

## Step 2 — Verify LangSmith credentials

Check `printenv LANGSMITH_API_KEY | head -c 6`. If empty, ask the user to set
it before continuing — deploys will fail without it. Also confirm the
workspace has LangSmith Deployment enabled (Plus/Enterprise plan).

## Step 3 — Verify build readiness

Run `ls langgraph.json` in the user's project root. If missing, defer to
`langgraph-new` first.

## Step 4 — Resolve options (defaults if unspecified)

| Option        | Default                                | Override flag      |
| ------------- | -------------------------------------- | ------------------ |
| Deployment    | first deployment in workspace          | `--deployment <name>` |
| Config        | `langgraph.json`                       | `-c <path>`        |
| Wait          | true                                   | `--no-wait`        |
| Tag           | git short SHA                          | `-t <tag>`         |

Use defaults unless the user specifies otherwise.

## Step 5 — Run

```bash
langgraph deploy [resolved flags]
```

Stream the build/deploy logs back to the user. After success, surface the
deployment URL printed by the CLI.

## Step 6 — Suggest verification

Recommend the user open LangSmith → Deployments to confirm the new revision is
serving, and use the `langsmith-traces` skill to verify traffic is being
recorded once they hit the endpoint.

For platform-specific questions (custom domains, autoscaling, secrets), defer
to `langchain-docs-search`.
