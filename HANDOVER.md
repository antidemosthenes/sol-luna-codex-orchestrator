# Handover for improving subagent dispatch

## Objective

Make delegation predictable for users who install this repository's templates and then give Codex an ordinary task. The parent should recognize a useful split early, select an appropriate worker, and review the result. Keep a routine sequential task cheap to run.

## Finding

The [parent instructions](templates/AGENTS.md) say to delegate when doing so saves context, time, or cost. They introduce the [delegation skill](templates/.agents/skills/delegate-work/SKILL.md) when the parent is already packaging a task. Neither gives the parent a concrete trigger for making the first dispatch. Direct execution of a substantial multi-part audit is therefore consistent with much of the current wording.

A separate issue arose during diagnosis. The skill and both worker profiles were present on disk. The session's skill catalog omitted the skill, and the launch interface exposed neither a named-profile selector nor a Luna model override. No Luna launch was attempted. That observation establishes what one session exposed. Account-wide access and behavior in a fresh session remain unverified.

## Proposed changes

1. **Give the parent an explicit dispatch trigger.** Replace the general advice in the Delegation section of the parent template with the passage below. Consolidate duplicate routing guidance in Parent-aware routing and Escalation so later edits have one authoritative role table.

   > At the start of a multi-part implementation, audit, or investigation, identify any independent task with a clear deliverable. When an eligible worker can complete it and the parent has useful work to do in parallel, dispatch the worker before starting the parent's portion. Use the delegate-work skill to brief the worker. Keep tightly coupled edits and single sequential operations with the parent. The parent reviews the worker's evidence and integrates the result.

2. **Keep worker selection and handoff details in the skill.** Use Luna High for bounded routine implementation and inspection. Under Astra, use Sol Medium for harder independent investigation. A Sol parent can handle that investigation directly. Give each worker one outcome, the minimum context, and an acceptance check. Give parallel writers distinct files. Retain the existing [structured handoff](templates/.agents/skills/delegate-work/references/work-package.md) for tasks that benefit from it.

3. **Document the fallback once.** When the skill is absent from the session catalog, check the active checkout for `.agents/skills/delegate-work/SKILL.md` and read it if available. Use a configured profile or an explicitly supported model and effort selector. If neither selects the intended worker, proceed locally when feasible. In the handover, distinguish direct execution by choice, an unavailable selector, and a rejected launch whenever that distinction affected the task.

4. **Add two examples and a smoke test to the [README](README.md).** Use the examples below to teach the decision. Put the smoke test after installation so maintainers can verify dispatch without making it a prerequisite for every task. The existing profile definitions already specify the intended models and reasoning effort.

| Ordinary user request | Expected parent decision |
| --- | --- |
| Audit a shutdown handler and independently review the confirmation dialog. | Assign the bounded dialog review to an eligible worker while the parent checks process lifetime and cleanup. Integrate both findings. |
| Rerun the existing CI suite and report the result. | Run the suite once with its configured test workers. A duplicate agent run adds cost and can introduce contention. |

## Verify in a consuming project

Install the contents of `templates/`, including the hidden directories, in a disposable project with a README. Start a fresh Codex chat with that project as its primary folder. The source checkout stores distributable files under `templates/`, while discovery in the consuming project uses the installed paths.

First ask the parent to identify the active project root, the skill visible in the current session, and the supported worker selection controls. Record file presence and session discovery separately.

Then use this one-time, read-only prompt:

> Dispatch luna-worker to read the first heading in the root README.md and return it. Wait for the worker and report the heading and which worker you requested. If the host cannot select that worker, identify the missing control or actual launch error.

A returned worker result verifies execution. Record the effective model and effort if the host exposes them. Otherwise record only what was requested. Finally, try the two ordinary requests above without mentioning subagents. These cases check whether the parent applies the new trigger on its own. A successful forced dispatch alone does not establish that behavior.

## Handover status

These are proposed edits for the original template repository. The next implementation belongs in the parent template, delegation skill, and README. The dispatch smoke test and ordinary-prompt acceptance checks remain pending.
