# Routines — Structured Workflow Graphs

## Purpose
Routines are directed workflow graphs that orchestrate agents, gates, councils, and terminal steps. They provide deterministic, auditable, and maintainable execution paths instead of relying on a single unstructured agent pass. Routines are the primary way to express complex, multi-step business logic in Nenjo.

## Core Model

A routine is a directed graph consisting of:

- **Steps** — Individual units of work (agent, gate, council, etc.)
- **Edges** — Connections that define execution flow and dependencies
- **Trigger** — What causes the routine to start (`task` or `cron`)
- **Flow state** — Runtime state for activated entries, steps, edges,
  handoffs, joins, retries, and terminals
- **Optional metadata** — Custom configuration per routine

Platform routine graphs are mostly acyclic. The one allowed cycle shape is a
bounded gate failure loop: a `gate` step may send an `on_fail` edge back to an
earlier step. The runner uses `metadata.max_attempts` when present and defaults
to 3 attempts when it is omitted. If the retry budget is exhausted, the routine
fails directly with a structured `retry_exhausted` result.

## Triggers

| Trigger | Description                              | Common Use Case                     |
|---------|------------------------------------------|-------------------------------------|
| `task`  | Started when a project task is dispatched to the routine | Project work that needs a reusable workflow |
| `cron`  | Started by a schedule on the routine | Periodic maintenance, monitoring, reports |

Use `task` for normal user/project work. Use `cron` only when the routine owns a
schedule; include schedule metadata when configuring cron behavior.

## Step Types

| Step Type      | Description                                                                 | Typical Use |
|----------------|-----------------------------------------------------------------------------|-------------|
| `agent`        | Executes one assigned agent; requires `agent`; use `config.instructions` for the step task | Core work   |
| `gate`         | Evaluates prior evidence and branches; requires `agent`; use gate criteria and `config.instructions` | Quality control, validation |
| `council`      | Runs one assigned council; requires `council`                               | Review, synthesis, voting |
| `terminal`     | Successful end of the routine                                               | Success stop |
| `terminal_fail`| Failed end of the routine                                                   | Explicit failed stop after an escalation path |

## Failure & Verdict Handling

**Important runtime behavior:**

- Every **agent step** is exposed to `route_next_steps`. This is the agent
  step's terminal tool: it records `verdict`, `reasoning`, `output`, and, on
  pass, the decomposed task and handoff for each downstream edge.
- Every **gate step** is exposed to `pass_verdict`. This records a structured
  gate pass/fail verdict and drives `on_pass` / `on_fail` routing.
- Agent and gate steps receive the local step instructions from
  `step.config.instructions` when present. Use this field to tell the assigned
  agent exactly what work to perform for that step, what inputs or upstream
  evidence to inspect, and what output or verdict standard to apply.
- Step config is intentionally narrow. Use only `config.instructions` and,
  when the prompt explicitly references it, `config.metadata`. Do not add
  top-level `config.inputs`, `config.evaluation_criteria`, `config.max_attempts`,
  or similar fields; they do not control execution. Retry budgets belong on
  `on_fail` edge `metadata.max_attempts`.

**Failure semantics differ by step type:**

- **Agent Step Failure**: If an agent step fails (either by returning
  `route_next_steps` with `verdict="fail"` or by throwing an error), the
  **entire routine fails** immediately. There is no `on_fail` edge for agent
  steps.
- **Gate Step Failure**: If a gate step fails (returns a failure verdict), execution follows the explicitly defined `on_fail` edge. This allows graceful degradation, bounded rework, escalation, or routing to a failure path.

This distinction ensures that critical work steps are treated as atomic (failure = routine failure), while gates can implement sophisticated error handling and branching.

## Edge Metadata And Handoffs

Edges carry authoring metadata that shapes runtime handoffs:

- `metadata.purpose` explains why the route exists.
- `metadata.handoff_instructions` tells the source agent what information to
  pass to the target through `route_next_steps`.
- `metadata.max_attempts` is used only on gate `on_fail` retry edges.

Handoff instructions should request actual state for the downstream step:
findings, decisions, artifact refs, changed files, failed criteria, unresolved
questions, constraints, or implementation notes. Do not use handoff metadata to
repeat the target step instructions.

When an agent step activates multiple outgoing edges, the source agent must
provide one `next_steps` item for each downstream route. Each item should follow
that edge's handoff instructions and include the target step slug.

When a target step has multiple activated incoming edges, it is an all-success
join. The target receives one structured handoff block per activated incoming
edge and should keep source steps distinct while synthesizing the combined
state.

## Common Workflow Patterns

Routines are typically built using well-known patterns:

- **Gated Pipeline** — Linear flow with validation gates at key stages
- **Fan Out** — Parallel independent work followed by a join/review
- **Linear Pipeline** — Simple sequential execution
- **Review Pipeline** — Generation → Review → Approval/Rejection path

## Graph Rules

- Routine graphs must be acyclic except bounded gate failure loops.
- One or more entry steps should be present. Use `entry_steps` to name them.
  Multiple entry steps start as parallel branches.
- Every routine step has a stable `slug`. Edge `source_step`, edge
  `target_step`, and `entry_steps` should reference those step slugs.
- Terminal outcomes must be explicit steps. Add a step with `step_type:
  "terminal"` or `step_type: "terminal_fail"` and target that step slug from
  edges; do not target an undeclared slug such as `terminal`.
- Every non-terminal step must have at least one outgoing edge.
- Terminal steps must not have outgoing edges.
- Every step must be reachable from at least one entry step.
- Multiple activated incoming edges into a step are an all-success join; the
  target step runs only after all upstream branches required for that path pass.
- Every routine must have at least one reachable terminal step: `terminal` or
  `terminal_fail`.
- Edge `condition` values are `always`, `on_pass`, or `on_fail`.
- Gate outgoing edges must use `on_pass` and/or `on_fail`; do not use `always`
  from a gate.
- Agent and gate steps must set `agent` to the executor agent slug. Council
  steps must set `council` to the council slug. For scheduled routines, set the
  routine trigger to `cron`; the graph still uses ordinary routine nodes.
- Agent and gate steps should set `config.instructions` with step-specific task
  instructions. The text should be concrete enough that the assigned agent can
  execute the step without inferring its task from only the routine name, step
  slug, or general agent prompt.
- Edges should use `metadata.purpose` to explain why the route exists and
  `metadata.handoff_instructions` to tell the source agent what information to
  pass to that target through `route_next_steps`. At runtime, joined target
  steps receive one structured handoff block for each activated incoming edge.
- If an `on_fail` edge creates a retry cycle, the source step must be a `gate`
  and the retry remains bounded by `metadata.max_attempts` or the runner default.
- Do not add an `on_exhausted` branch. Retry exhaustion fails the routine
  directly and records the exhausted edge in the result data.

## Bounded Rework Pattern

Express retries only as a bounded gate failure loop:

1. `implement -> review_gate`
2. `review_gate on_pass -> done`
3. `review_gate on_fail -> implement`, optionally with `metadata.max_attempts`
4. If the retry budget is exhausted, the routine fails directly

The gate owns the retry decision. Agent steps do not use `on_fail` retry edges;
agent step failure fails the routine immediately.

## Runtime Behavior

When a routine is triggered:

1. The trigger (task or cron) provides initial context
2. Entry steps become runnable
3. Agent steps execute and call `route_next_steps` with downstream handoffs
4. Gates evaluate output with `pass_verdict` and activate `on_pass` or `on_fail`
5. Councils run multi-agent collaboration when reached
6. Join targets wait for every activated incoming branch to pass
7. The routine ends at a `terminal` (success), `terminal_fail` (failure), agent
   step failure, or retry exhaustion

Routines provide strong observability — every step transition, gate decision, and council output is recorded.

## Pitfalls to Avoid
- Creating overly complex graphs with too many steps
- Using gates without clear acceptance criteria
- Forgetting to define both success and failure terminal paths
- Pointing an edge at `terminal` or `failed` without adding a matching terminal
  or terminal_fail step to the graph
- Creating cycles from non-gate steps, `on_pass` edges, or unbounded `on_fail`
  edges
- Omitting handoff instructions, forcing downstream steps to infer needed state
- Writing generic handoffs that restate target instructions instead of passing
  source-specific evidence

## Best Practices
- Start simple (Linear Pipeline or Gated Pipeline)
- Use councils only when multiple perspectives or synthesis are genuinely needed
- Keep individual steps focused — move complex logic into abilities or sub-agents
- Treat each edge as a state handoff contract, especially for fan-out and joins
