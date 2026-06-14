# Workflow Pattern Cookbook

## Purpose

Use this cookbook when translating a workflow pattern into concrete Nenjo
resources. It complements `classification.workflow_patterns`, which chooses the
pattern, and `resources.routines`, which defines exact routine mechanics.

## Selection Rule

Use routines when the workflow shape itself must be durable, scheduled,
auditable, or deterministic. Use agents, abilities, and councils when the
workflow shape should remain flexible and intent-driven.

## Prompt Chaining

Intent: run a fixed sequence of transformations, analyses, or handoffs.

Routine shape:

```text
entry_steps: [draft]
draft -> review -> finalize -> done
```

Use a routine when each phase needs its own owner, model, prompt, audit trail,
or gate. Use one agent with internal phases when the sequence is lightweight and
does not need separate execution records.

## Routing

Intent: choose one of several paths based on intent, context, risk, or evidence.

Implementation options:

| Option | Use When | Tradeoff |
|---|---|---|
| Router agent | Inputs are fuzzy or conversational | Flexible, but less deterministic and less auditable |
| Council | Route choice benefits from multiple perspectives | Better judgment, but heavier |
| Routine branch | Paths are known and route choice must be audited | Strong graph trail, but topology must be authored up front |
| Domain switch | User chooses an operating mode or permission boundary | Clear mode boundary, not a general router |

Routine branches should be explicit business-process branches, not hidden
model-driven intent routing. For agent-driven branch choice, prefer a router
agent or council.

## Parallelization

Intent: run independent work in parallel, then join or synthesize.

Routine shape with parallel entry steps:

```text
entry_steps: [research_api, research_ui]
research_api -> synthesize
research_ui -> synthesize
synthesize -> done
```

Routine shape with agent fan-out:

```text
entry_steps: [decompose]
decompose -> backend
decompose -> frontend
backend -> synthesize
frontend -> synthesize
synthesize -> done
```

Multiple entry steps start in parallel. Multiple activated incoming edges are an
all-success join. Multiple outgoing edges from an agent are deterministic
fan-out; the agent receives `route_next_steps` and must provide a task for every
downstream edge.

Use a routine when branch state and joins must be visible. Use sub-agents when
parallel work is ad hoc. Use a council when the branches are independent
perspectives that require synthesis.

## Orchestrator-Workers

Intent: one owner decomposes work, specialists execute, and a final step
synthesizes results.

Routine shape:

```text
entry_steps: [plan]
plan -> worker_a
plan -> worker_b
worker_a -> synthesize
worker_b -> synthesize
synthesize -> done
```

Use a routine when worker lanes are predictable enough to model as steps. Use a
single orchestrator agent with abilities or sub-agents when decomposition is
dynamic. Use a council when the leader/member relationship and synthesis
judgment are the core value.

## Evaluator-Optimizer

Intent: generate work, evaluate it, and retry bounded rework until it passes.

Routine shape:

```text
entry_steps: [implement]
implement -> review_gate
review_gate on_pass -> done
review_gate on_fail -> implement [metadata.max_attempts = 3]
```

Use a gate for the evaluator. Gates call `pass_verdict`. The `on_fail` edge may
loop back to an earlier step and is bounded by `metadata.max_attempts` or the
runner default. Do not author `on_exhausted`; retry exhaustion fails the routine
directly with a structured `retry_exhausted` result.

Use an agent self-reflection loop only when audit and deterministic retry
budget are not important.

## Council Step

Intent: insert structured multi-agent judgment inside an otherwise deterministic
workflow.

Routine shape:

```text
entry_steps: [prepare]
prepare -> review_council
review_council -> gate
gate on_pass -> done
gate on_fail -> failed
```

Use this when the macro process is deterministic but one step benefits from
multiple perspectives, critique, voting, or synthesis. Keep the council as a
single routine step unless its internal branches need graph-level audit.

## Autonomous Agent

Intent: give one agent a goal and let it decide tool use and intermediate
steps.

Prefer a single agent with focused abilities, domains, tools, and knowledge.
Wrap it in a routine only when the agent needs scheduled execution, explicit
handoffs, gate checkpoints, or durable audit around a larger process.

## Approval Or Escalation

Intent: require explicit approval, external evidence, or a known failure path.

Routine shape:

```text
entry_steps: [produce]
produce -> approval_gate
approval_gate on_pass -> done
approval_gate on_fail -> failed
```

Use a gate when pass/fail criteria can be evaluated by an assigned agent. Use
`terminal_fail` for explicit authored failure or escalation outcomes. Retry
exhaustion is not an authored failure branch; it fails the routine directly.

## Authoring Checklist

- Choose the least complex resource shape that preserves required guarantees.
- Name one or more `entry_steps`.
- Assign `agent` on every `agent` and `gate` step.
- Assign `council` only on `council` steps.
- Add edge purpose metadata for fan-out edges so `route_next_steps` can show
  each downstream route clearly.
- Use multiple incoming edges only when the target is an all-success join.
- Use gate `on_fail` retry loops only when bounded rework is intentional.
- Add `max_attempts` only when the default retry budget should change.
- Do not use `on_exhausted`.
- Verify reachable terminals, no terminal outgoing edges, and no cycles except
  bounded gate `on_fail` loops.
