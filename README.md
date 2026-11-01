# Astra/Sol + Luna Codex Orchestrator

A lightweight Codex multi-agent setup that keeps the parent focused on judgement while delegating bounded implementation when it actually helps.

Use Astra or Sol as the parent and Luna for bounded work when delegation saves context, time or cost.

## Model targets

Updated November 1, 2026. These are explicit model IDs, not aliases that automatically track future releases. Low, Medium and High refer to reasoning effort.

| Role | Model ID | Reasoning effort | Worker profile |
|---|---|---|---|
| Larger / agentic parent | `gpt-6-astra` | `low` by default | Select in your Codex client |
| Smaller / straightforward parent | `gpt-6.1-sol` | Keep the selected effort; increase when justified | Select in your Codex client |
| Tiny edits and normal bounded implementation | `gpt-6-luna` | `high` | `luna-worker` |
| Harder bounded work under Astra | `gpt-6.1-sol` | `medium` | `sol-escalation` |
| Exceptional Astra parent reasoning | `gpt-6-astra` | `medium` or `high` | Adjust the parent when supported |

The IDs and reasoning settings follow the [official model catalog](https://developers.openai.com/api/docs/models) and [Codex subagent configuration](https://learn.chatgpt.com/docs/agent-configuration/subagents). Parent selection is manual; the worker profiles configure subagents, not the active parent.

> **The active parent owns the global problem. Delegate bounded work only when it saves context, time or cost.**

GPT-5.3 Codex Spark is no longer part of this setup. Tasks previously assigned to Spark now use `luna-worker`.

## Preserved older setups

These branches document historical configurations and may refer to retired models:

- [`v1-sol-luna`](https://github.com/breko861-hash/sol-luna-codex-orchestrator/tree/v1-sol-luna) - original Sol + Luna setup
- [`v2-sol-spark-luna-terra`](https://github.com/breko861-hash/sol-luna-codex-orchestrator/tree/v2-sol-spark-luna-terra) - Sol Medium parent, Spark/Luna workers, Terra High escalation
- [`v3-astra-low-parent`](https://github.com/breko861-hash/sol-luna-codex-orchestrator/tree/v3-astra-low-parent) - Astra Low parent, Spark/Luna workers and Sol Medium escalation

## Parent-aware routing

### When Astra Low is the parent

```text
Astra Low
    |
    |-- trivial work; handoff adds overhead ------> handle directly
    |
    |-- tiny delegated edits / normal work -------> Luna High
    |
    |-- harder bounded work ----------------------> Sol Medium
    |
    `-- exceptional parent-level problem --------> Astra Medium/High
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

Use **Astra Low** as the parent for larger/orchestrated work, or **Sol** as the parent for smaller/straightforward work.

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

Confirm that your Codex client discovers `luna-worker` and `sol-escalation` and that your account can access their models. Use the named profile, or explicitly request its model and reasoning effort with supported runtime controls. A generic subagent can inherit the parent's settings, which defeats the intended cost saving.

### Updating an existing installation

Merge the updated `AGENTS.md` and replace the `delegate-work` skill and both worker profiles with these versions. Remove the old `.codex/agents/spark-worker.toml` from the target project and remove any custom routing or configuration entries pointing to it. Copying the new template alone will not remove an old installed profile.

Update any locally pinned parent model to `gpt-6.1-sol` for Sol or `gpt-6-astra` for Astra. Keep unrelated project configuration intact.

## What each model does

### Astra Low

Use as the parent when the task benefits from planning, decomposition, multi-step coordination, review and integration. Delegate bounded implementation when the handoff has a clear benefit; handle trivial work directly.

### Sol as parent

Use for smaller or straightforward coding work where spawning workers would add unnecessary overhead. Sol may still delegate to Luna when doing so clearly saves context, time or cost.

For harder work, the active Sol parent handles the problem directly. Raise Sol reasoning only when justified rather than routing through a separate Sol worker.

### Luna High

Use for tiny deterministic edits and normal bounded implementation once the active parent has resolved the important product and architecture decisions. Both use the same `luna-worker` profile.

### Sol Medium worker

When Astra is the parent, use Sol Medium if Luna is not enough for a bounded problem such as harder debugging, investigation, subtle state behaviour or implementation that needs materially more independent reasoning.

If the problem becomes a genuinely global architecture, security or product decision, return it to the active parent instead of silently taking ownership of it.

### Astra Medium/High

Reserve higher Astra reasoning for exceptional parent-level problems where Astra Low is not enough, especially consequential architecture, security-sensitive design, difficult cross-system reasoning or unresolved failures that change the overall approach.

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

You should not need to append orchestration instructions to every prompt.

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
3. Use Astra Low for larger orchestrated work and Sol for smaller straightforward work.
4. Delegate only when it provides a real benefit.
5. Keep worker context minimal and sufficient.
6. Use Luna High for tiny delegated edits and normal bounded implementation.
7. When Astra is the parent, use Sol Medium only when the bounded task really needs it.
8. Verify proportionately to risk.
9. Define completion clearly enough that the parent keeps going until the requested outcome actually works.
10. Preserve older setups in version branches instead of forcing one configuration onto every model generation.
