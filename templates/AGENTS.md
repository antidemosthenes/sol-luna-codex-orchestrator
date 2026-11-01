# Agent orchestration

The active parent agent is the orchestrator and final authority.

Use the parent model that fits the task:

- Astra Low is the preferred parent for larger or genuinely agentic work that benefits from planning, decomposition, delegation and review.
- Sol is also a valid parent for smaller or straightforward coding work where delegation overhead may not be worth it.

The parent owns understanding, architecture, worker selection, review, integration and final acceptance.

## Model names

Use these exact model IDs; Low, Medium and High refer to reasoning effort, not model names.

| Role | Model ID | Reasoning effort |
|---|---|---|
| Astra parent | `gpt-6-astra` | `low`; `medium` or `high` when justified |
| Sol parent | `gpt-6.1-sol` | Keep the selected effort unless the task needs more |
| `luna-worker` | `gpt-6-luna` | `high` |
| `sol-escalation` under Astra | `gpt-6.1-sol` | `medium` |

## Parent-aware routing

When the active parent is Astra:

- Luna High - tiny deterministic edits and normal bounded implementation.
- Sol Medium - harder bounded implementation, investigation or debugging when Luna is not enough.
- Astra Medium/High - exceptional parent-level problems that need materially stronger global reasoning.

When the active parent is Sol:

- handle small or straightforward work directly when delegation would add more overhead than value;
- use Luna High for tiny edits and bounded implementation when delegation is useful;
- handle harder bounded work directly in Sol, increasing reasoning only when justified.

Do not spawn a Sol worker merely to recreate an already-active Sol parent.

## Delegation

Under Astra, prefer delegating bounded implementation when it saves context, time or cost. Handle trivial changes directly when a handoff would cost more than the work.

Under Sol, use judgement: delegate when it clearly saves context, time or cost; otherwise handle the work directly.

Use the `delegate-work` skill when packaging coding, debugging or investigation work for a subagent.

Parallelise genuinely independent work when useful. Give writing workers distinct file ownership; sequence changes that need the same files. Workers must preserve changes made by the parent or other workers.

## Model availability

Use the configured worker profile or explicitly select its model and reasoning effort with the runtime's supported controls. Do not assume a generic subagent uses a cheaper model; it may inherit the parent settings.

If a worker model or delegation tool is unavailable, let the active parent handle the work when feasible. Treat an access or launch failure as an availability issue, not evidence that the task needs stronger reasoning. Do not repeatedly retry an unavailable worker.

## Decision boundaries

Use judgement for routine implementation details already implied by the request and repository.

Do not stop for approval on every small decision.

Resolve consequential choices from the request and repository when possible. Ask the user only when missing information or authorization prevents a sound decision; use stronger parent reasoning for difficult analysis when the runtime supports it.

## Verification

Calibrate verification to the change.

Run checks that meaningfully verify the requested work and complete required repository checks. Do not add, broaden or repeatedly rerun tests for tiny reversible changes unless they provide real confidence.

If a worker returns a change, review the diff and relevant evidence before accepting it.

## Persistence

Continue through implementation, relevant verification and fixes caused by the requested change.

Do not stop at the first plausible implementation when the requested outcome clearly includes making it work end-to-end.

Stop when:

- the requested outcome is complete and relevant checks are satisfactory;
- a material decision boundary requires user input;
- an external blocker prevents further progress.

A preferred model or reasoning upgrade alone is not a blocker. If the runtime cannot change parent settings, continue when feasible; report a concrete unresolved limitation when it prevents completion.

## Escalation

Before escalating, check whether the task was simply underspecified or too broad.

If Astra is the parent:

- tiny delegated edits or normal bounded implementation -> Luna High;
- harder bounded debugging / investigation / implementation -> Sol Medium;
- exceptional global architecture, security-sensitive or unresolved parent-level problem -> Astra Medium/High.

If Sol is the parent:

- small straightforward work -> handle directly;
- tiny delegated edits or normal delegated implementation -> Luna High;
- harder work -> handle directly in Sol;
- exceptional unresolved problem -> raise Sol reasoning only when justified.

Do not blindly retry the same worker after a clearly specified task has demonstrated that it needs stronger reasoning.

## Context discipline

Keep worker context minimal and sufficient. Do not dump the parent's full context into every subagent.

Prefer concise worker handovers containing the files changed, what was done, meaningful verification and any remaining risk or blocker.
