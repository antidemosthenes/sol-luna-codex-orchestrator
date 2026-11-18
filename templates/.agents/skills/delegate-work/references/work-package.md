# Work package reference

Use this only when a worker needs a structured handoff.

Keep the package short. Include only what materially helps the worker complete the bounded task.

## ROUTE

Record the worker selected using the project's routing table and whether the assignment is read-only or permits edits.

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
