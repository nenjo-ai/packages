# Routine Flow Authoring

## Purpose

Use routine flow authoring when turning a workflow design into a concrete
routine graph with explicit runtime state, edge metadata, and handoff contracts.

This doc complements `design.workflows`, which chooses the workflow shape,
`resources.routines`, which defines routine mechanics, and
`resources.executions`, which explains runtime state.

## Flow State Model

A routine run advances through graph state, not just a list of steps:

- entry activation: one or more `entry_steps` become runnable;
- step execution: an agent, gate, or council produces a recorded result;
- edge activation: a passed source step activates matching outgoing edges;
- handoff creation: an agent step calls `route_next_steps` with one handoff per
  downstream edge;
- join readiness: a target with multiple activated incoming edges waits until
  every required upstream handoff or result is present;
- gate routing: a gate calls `pass_verdict`, then activates `on_pass` or
  `on_fail`;
- retry accounting: a gate `on_fail` loop consumes the edge retry budget;
- terminal completion: the run reaches `terminal`, `terminal_fail`, or fails
  directly because an agent step failed or retry budget was exhausted.

Design the graph so each transition has enough state for the next step to run
without guessing what happened upstream.

## Authoring Sequence

1. Name the routine trigger: `task` for project task dispatch or `cron` for a
   scheduled routine.
2. List steps with stable slugs, step types, and owners.
3. Write `config.instructions` for every `agent` and `gate` step.
4. Draw edges with source, target, and condition.
5. Add edge metadata that explains why the edge exists and what the source must
   hand off to the target.
6. Mark one or more `entry_steps`.
7. Add explicit `terminal` and, when useful, `terminal_fail` steps.
8. Verify reachability, joins, retry loops, and terminal paths before writing.

## Step Instructions

Use `step.config.instructions` for local work instructions. Good step
instructions tell the assigned executor:

- the local objective;
- which task, project, Library, or upstream evidence to inspect;
- what output or verdict to produce;
- what acceptance standard applies;
- what downstream consumers will need.

Keep step config narrow. Use only `instructions` and optional `metadata` when
the prompt explicitly consumes `{{ routine.step.metadata }}`. Do not put
`inputs`, `evaluation_criteria`, `max_attempts`, or routing fields in step
config. Retry budgets belong on `on_fail` edge `metadata.max_attempts`.

## Edge Metadata

Use edge metadata to define the handoff contract between steps.

`metadata.purpose` should explain why the route exists in business or workflow
terms. It helps the source agent distinguish downstream paths when
`route_next_steps` is available.

`metadata.handoff_instructions` should tell the source agent what actual
information to include in the handoff for that target. It should not repeat the
target step instructions. It should specify the needed evidence, decisions,
artifact refs, constraints, unresolved questions, or implementation notes.

Use `metadata.max_attempts` only on a gate `on_fail` edge that loops back for
bounded rework.

Example edge metadata:

```yaml
metadata:
  purpose: Send implementation evidence to review.
  handoff_instructions: >-
    Include the changed files, tests run, unresolved risks, and any acceptance
    criteria that still need reviewer attention.
```

For fan-out, each outgoing edge should have different handoff instructions:

```yaml
- source_step: plan
  target_step: backend
  condition: always
  metadata:
    purpose: Route backend implementation work.
    handoff_instructions: >-
      Provide backend requirements, API contracts, data model notes, and
      dependencies on frontend or worker behavior.
- source_step: plan
  target_step: frontend
  condition: always
  metadata:
    purpose: Route frontend implementation work.
    handoff_instructions: >-
      Provide UI states, user flows, API calls, validation rules, and visual or
      interaction constraints.
```

## Handoff Rules

When an agent step passes, it calls `route_next_steps` as its final action.
The call records the step verdict, reasoning, output, and one `next_steps` item
for each activated downstream route.

Each `next_steps` item should include:

- `target_step`;
- a concrete handoff for that target;
- enough context for the target to run without reading the source transcript as
  its primary input.

The handoff should contain state, not instructions alone. Prefer concrete
outputs such as findings, decisions, artifact refs, changed files, failed
checks, constraints, and open questions.

## Joins

A step with multiple activated incoming edges is an all-success join. It runs
only after every required upstream branch reaches that target.

For join targets:

- each incoming edge should define its own handoff contract;
- the target step instructions should say how to compare, merge, or synthesize
  incoming handoffs;
- the target should preserve source identity when evidence conflicts.

Example join instruction:

```text
Synthesize the backend and frontend handoffs into one release plan. Keep source
steps distinct. Call out conflicts, missing dependencies, and the smallest safe
ship path.
```

## Gate And Retry Flow

Gates call `pass_verdict`, not `route_next_steps`. A gate pass activates
`on_pass`; a gate fail activates `on_fail`.

Use a retry loop only for bounded rework:

```text
implement -> review_gate
review_gate on_pass -> done
review_gate on_fail -> implement [metadata.max_attempts = 3]
```

The retry edge should describe the repair handoff:

```yaml
metadata:
  purpose: Send failed review findings back for bounded rework.
  handoff_instructions: >-
    Include the failed criteria, required fixes, evidence gaps, and any reviewer
    notes the implementation step must address before trying again.
  max_attempts: 3
```

Do not add an `on_exhausted` edge. When the retry budget is exhausted, the run
fails directly with structured retry-exhausted result data.

## Verification Checklist

Before writing or updating a routine:

- every edge source and target references a declared step slug;
- every non-terminal step has at least one outgoing edge;
- every terminal step has no outgoing edges;
- every agent and gate step has an `agent` slug and `config.instructions`;
- every council step has a `council` slug;
- every meaningful edge has `metadata.purpose` and
  `metadata.handoff_instructions`;
- every fan-out edge has a distinct handoff contract;
- every join target has instructions for combining upstream handoffs;
- every cycle is a gate `on_fail` retry loop with bounded attempts;
- there is no `on_exhausted` edge;
- at least one terminal or terminal_fail step is reachable from each entry path.

