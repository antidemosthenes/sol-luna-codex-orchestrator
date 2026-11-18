# Astra/Sol + Luna Codex Orchestrator

A lightweight Codex multi-agent setup that keeps the parent focused on judgement while delegating bounded implementation when it actually helps.

Use Astra or Sol as the parent and Luna for bounded work when delegation saves context, time or cost.

## Model targets

Updated November 17, 2026. The model IDs are pinned to specific releases. The ChatGPT Work picker calls Astra’s lowest effort **Light**. In Codex TOML, write `model_reasoning_effort = "low"`. Likewise, **Extra High** is `xhigh`. Available levels depend on the model and client. [Reasoning levels](https://learn.chatgpt.com/docs/agent-configuration/subagents#choosing-models-and-reasoning) · [Codex configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference)

| Role | Model ID | Reasoning effort | Worker profile |
|---|---|---|---|
| Larger / agentic parent | `gpt-6.2-astra` | Any supported effort you select | Select in your Codex client |
| Smaller / straightforward parent | `gpt-6.2-sol` | Keep the selected effort; increase when justified | Select in your Codex client |
| Tiny edits and normal bounded implementation | `gpt-6-luna` | `high` | `luna-worker` |
| Harder bounded work under Astra | `gpt-6.2-sol` | `medium` | `sol-escalation` |

See the [Codex model catalog](https://learn.chatgpt.com/docs/models?surface=cli) for model availability and [Codex subagent configuration](https://learn.chatgpt.com/docs/agent-configuration/subagents) for worker setup. Select the parent model in your Codex client. The worker profiles apply to subagents.

> **The active parent owns the global problem. Delegate bounded work only when it saves context, time or cost.**

GPT-5.3 Codex Spark is no longer part of this setup. Tasks previously assigned to Spark now use `luna-worker`.

## Preserved older setups

These branches document historical configurations and may refer to retired models:

- [`v1-sol-luna`](https://github.com/breko861-hash/sol-luna-codex-orchestrator/tree/v1-sol-luna) - original Sol + Luna setup
- [`v2-sol-spark-luna-terra`](https://github.com/breko861-hash/sol-luna-codex-orchestrator/tree/v2-sol-spark-luna-terra) - Sol Medium parent, Spark/Luna workers, Terra High escalation
- [`v3-astra-low-parent`](https://github.com/breko861-hash/sol-luna-codex-orchestrator/tree/v3-astra-low-parent) - Astra Low parent, Spark/Luna workers and Sol Medium escalation

## Parent-aware routing

### When Astra is the parent

```text
Astra (any supported parent effort)
    |
    |-- trivial work; handoff adds overhead ------> handle directly
    |
    |-- tiny delegated edits / normal work -------> Luna High
    |
    |-- harder bounded work ----------------------> Sol Medium
    |
    `-- global decisions and integration --------> handle directly in Astra
```

### When Sol is the parent

```text
Sol
    |
    |-- small / straightforward work ------------> handle directly
    |
    |-- tiny delegated edits / normal work -------> Luna High
    |
    `-- harder work ------------------------------> handle directly in Sol
```

If Sol itself is the active parent, do not spawn a Sol worker merely to recreate the parent. Increase Sol reasoning only when the task genuinely justifies it.

When Astra is the parent, choose Sol Medium directly for bounded work that clearly exceeds Luna's capability; there is no need to attempt every tier first. Return global architecture, product or security decisions to the active parent.

If a worker model or delegation tool is unavailable, the parent handles the work when feasible. An access or launch failure is not evidence that a stronger model is needed. If parent settings cannot be changed during a session, continue when feasible and report any concrete limitation that prevents completion.

## Quick start

Copy the contents of `templates/`, including the hidden `.agents` and `.codex` directories, into the root of your project.

Choose **Astra** at your preferred effort for larger orchestrated work, or **Sol** for smaller, straightforward work. For example, Astra at `xhigh` dispatches a routine inspection to Luna at `high` and a harder independent investigation to Sol at `medium`.

```text
your-repo/
├── AGENTS.md
├── .agents/
│   └── skills/
│       └── delegate-work/
│           ├── SKILL.md
│           └── references/
│               └── work-package.md
└── .codex/
    └── agents/
        ├── luna-worker.toml
        └── sol-escalation.toml
```

If your project already has an `AGENTS.md`, merge the orchestration guidance rather than replacing project-specific instructions.

### Check dispatch after installation

Start a fresh local Codex chat in the **consuming project**. This repository stores the installable files under `templates/`. Ask Codex which project root and AGENTS.md apply, whether `delegate-work` appears in the session skill catalog, and which named profiles or model and effort controls its launch tool exposes. Files on disk establish installation. The session controls establish which workers the parent can request.

If the CLI reports that project-local configuration is disabled because the project is untrusted, check project trust before diagnosing a missing profile. If the launch tool itself is absent, check whether `agents.enabled` is disabled in your effective Codex configuration.

In a project with a root README, try this one-time read-only check:

> Dispatch luna-worker to read the first heading in the root README.md. Wait for its result and report the heading and which worker you requested. If you cannot select it, identify the missing control or the launch error.

Check the worker thread or tool activity for a completed launch and returned result. The heading alone can also be read by the parent. Record the effective model and effort only if the client exposes them. To check Sol separately under Astra, repeat with `sol-escalation`.

Then try ordinary prompts on a project with the relevant code and tests:

| Request | Expected behavior |
|---|---|
| Audit the shutdown handler and independently review the confirmation dialog. | With Luna available, the parent dispatches the dialog inspection while it traces process cleanup, then reviews both findings. |
| Rerun the existing CI suite and report the result. | The parent runs the configured suite once. |

Local Codex can follow an applicable AGENTS.md instruction to delegate. ChatGPT Work has different delegation controls and most intelligence levels require an explicit request. See [subagent availability and triggering](https://learn.chatgpt.com/docs/agent-configuration/subagents#availability).

### Updating an existing installation

Merge the updated `AGENTS.md` and replace the `delegate-work` skill and both worker profiles with these versions. Remove the old `.codex/agents/spark-worker.toml` from the target project and remove any custom routing or configuration entries pointing to it. Copying the new template alone will not remove an old installed profile.

Update any locally pinned parent model to `gpt-6.2-sol` for Sol or `gpt-6.2-astra` for Astra. Keep unrelated project configuration intact.

## What each model does

### Astra as parent

Use as the parent when the task benefits from planning, decomposition, multi-step coordination, review and integration. Delegate bounded implementation when the handoff has a clear benefit; handle trivial work directly.

### Sol as parent

Use for smaller or straightforward coding work where spawning workers would add unnecessary overhead. Sol may still delegate to Luna when doing so clearly saves context, time or cost.

For harder work, the active Sol parent handles the problem directly. Raise Sol reasoning only when justified rather than routing through a separate Sol worker.

### Luna High

Use for tiny deterministic edits and normal bounded implementation once the active parent has resolved the important product and architecture decisions. Both use the same `luna-worker` profile.

### Sol Medium worker

When Astra is the parent, use Sol Medium if Luna is not enough for a bounded problem such as harder debugging, investigation, subtle state behaviour or implementation that needs materially more independent reasoning.

If the problem becomes a genuinely global architecture, security or product decision, return it to the active parent instead of silently taking ownership of it.

## Completion and verification

The active parent should continue through implementation, relevant verification and fixes caused by the requested change. It should not stop at the first plausible implementation when the requested outcome clearly means making the feature or fix work end-to-end.

Verification should be proportional to risk. Run meaningful required checks, but do not add or repeatedly rerun tests for tiny reversible changes just because a generic instruction says to test everything.

## Delegation skill

The [root skill](templates/.agents/skills/delegate-work/SKILL.md) handles worker selection and routing. Use a short prompt for simple work; a structured handoff is available in:

`templates/.agents/skills/delegate-work/references/work-package.md`

That keeps detailed instructions out of context until they are actually needed.

Each handoff should state the outcome, scope, necessary context and useful verification. Give parallel writing workers distinct file ownership and preserve changes made by others. The parent reviews each returned diff and verification result before integrating it.

## Normal prompts after setup

Once installed, prompts can stay focused on the actual requirement:

```text
Add passwordless login using magic links.

Keep the existing auth architecture and email provider.
Do not change the existing password login flow.
Done when the flow works end-to-end and the relevant checks pass.
```

In a local Codex project with the templates loaded, the parent checks ordinary multi-part tasks for independent work it can dispatch.

## Included files

- `templates/AGENTS.md`
- `templates/.agents/skills/delegate-work/SKILL.md`
- `templates/.agents/skills/delegate-work/references/work-package.md`
- `templates/.codex/agents/luna-worker.toml`
- `templates/.codex/agents/sol-escalation.toml`
- `examples/example-prompt.md`

## Philosophy

1. Keep repository-wide instructions short and durable.
2. Let the active parent use judgement instead of turning every task into a rigid recipe.
3. Use Astra for larger orchestrated work and Sol for smaller straightforward work.
4. Delegate only when it provides a real benefit.
5. Keep worker context minimal and sufficient.
6. Use Luna High for tiny delegated edits and normal bounded implementation.
7. When Astra is the parent, use Sol Medium only when the bounded task really needs it.
8. Verify proportionately to risk.
9. Define completion clearly enough that the parent keeps going until the requested outcome actually works.
10. Preserve older setups in version branches instead of forcing one configuration onto every model generation.
