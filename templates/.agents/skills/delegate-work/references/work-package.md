# Work package reference

Use this only when a worker needs a structured handoff.

Keep the package short. Include only what materially helps the worker complete the bounded task.

## ROUTE

Choose one:

- `luna-worker` (`gpt-6-luna`, `high`) - tiny deterministic edits and normal bounded implementation;
- `sol-escalation` (`gpt-6.1-sol`, `medium`) - harder bounded debugging, investigation or implementation when Astra is the active parent.

Select the named profile or explicitly set its model and reasoning effort with supported runtime controls; a generic worker may inherit the parent's settings.

If Sol is already the active parent, do not use `sol-escalation` merely to recreate the parent. Let Sol handle the harder bounded work directly.

## GOAL

One concrete outcome.

## CONTEXT

Only the repository and product context needed for this task.

## SCOPE

Files, components or behaviour the worker owns.

For parallel edits, assign distinct file ownership and require the worker to preserve changes made by others. Sequence tasks that need the same files or depend on another worker's result.

## CONSTRAINTS

Important interfaces, invariants, boundaries or behaviour that must remain intact.

Avoid long generic rule lists.

## DONE WHEN

State the observable completion condition.

## VALIDATION

Specify only checks that materially verify the change.

For tiny reversible edits, a focused inspection or narrow check may be enough. For risky changes, require the relevant tests, build, lint, type checks or smoke tests.

## RETURN

Ask for a concise handover containing:

1. files changed;
2. what changed;
3. meaningful verification and result;
4. any remaining risk, assumption or blocker.

## Escalation

If a worker fails, first decide whether the task was unclear or too broad.

If Astra is the active parent and the package was already clear:

- Luna High -> Sol Medium;
- Sol Medium -> return to the Astra parent and consider Astra Medium/High for a genuinely global or consequential problem.

If Sol is the active parent and the package was already clear:

- Luna High -> direct Sol handling;
- raise Sol reasoning only if the problem genuinely warrants it.

Do not force every task through every tier.

An unavailable model or failed launch is not a reasoning failure. Let the parent handle the task when feasible rather than repeatedly retrying the unavailable worker. Change parent reasoning only if supported; if completion is blocked, report the specific limitation.
