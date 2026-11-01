---
name: delegate-work
description: Delegate bounded coding, debugging or investigation tasks to a cheaper worker when the handoff saves context, time or cost.
---

# Delegate Work

Use this skill when bounded coding, debugging or investigation work benefits from a worker. Handle trivial work directly when a handoff would cost more than the work.

If Astra is the active parent:

1. Pick the cheapest worker likely to finish the bounded task reliably:
   - `luna-worker`: `gpt-6-luna`, `high` effort, for tiny deterministic edits and normal implementation.
   - `sol-escalation`: `gpt-6.1-sol`, `medium` effort, for harder bounded debugging, investigation or implementation.
2. Give the worker one clear outcome and only the context it needs.
3. Define what completion looks like and what verification is actually useful.
4. If the worker fails, check task clarity before escalating. Do not loop blindly.

If Sol is the active parent:

- use `luna-worker` when delegation clearly saves context, time or cost;
- otherwise handle the bounded work directly;
- do not spawn `sol-escalation` merely to recreate the active Sol parent.

Select the configured worker profile or explicitly request its model and effort with the runtime's supported controls. A generic subagent may inherit the parent settings. If the model or delegation tool is unavailable, use direct parent handling when feasible instead of repeatedly retrying the launch.

Give parallel writing workers distinct file ownership and tell them to preserve other changes. Review the returned diff and verification before integrating it.

For a structured handoff, read [the work-package reference](references/work-package.md) when needed; a short task can use a concise prompt with the same essential information.
