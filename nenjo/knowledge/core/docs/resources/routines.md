# Routines — Structured Workflow Graphs

## Purpose
Routines are directed workflow graphs that orchestrate agents, gates, councils, and terminal steps. They provide deterministic, auditable, and maintainable execution paths instead of relying on a single unstructured agent pass. Routines are the primary way to express complex, multi-step business logic in Nenjo.

## Core Model

A routine is a directed graph consisting of:

- **Steps** — Individual units of work (agent, gate, council, etc.)
- **Edges** — Connections that define execution flow and dependencies
- **Trigger** — What causes the routine to start (`task` or `cron`)
- **Optional metadata** — Custom configuration per routine

Platform routine graphs are mostly acyclic. The one allowed cycle shape is a
bounded gate failure loop: a `gate` step may send an `on_fail` edge back to an
earlier step. The runner uses `metadata.max_attempts` when present and defaults
to 3 attempts when it is omitted. Use `on_exhausted` to route retry exhaustion
to a terminal failure or escalation step when needed.

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
| `agent`        | Executes one assigned agent; requires `agent`                               | Core work   |
| `gate`         | Evaluates prior evidence and branches; requires `agent`; use gate criteria in `config` | Quality control, validation |
| `council`      | Runs one assigned council; requires `council`                               | Review, synthesis, voting |
| `terminal`     | Successful end of the routine                                               | Success stop |
| `terminal_fail`| Failed end of the routine                                                   | Failed stop after escalation or exhausted retries |

## Failure & Verdict Handling

**Important runtime behavior:**

- Every **agent step** and **gate step** is automatically exposed to a `pass_verdict` tool.
- This tool allows the agent to explicitly return a structured pass/fail verdict along with a reason.

**Failure semantics differ by step type:**

- **Agent Step Failure**: If an agent step fails (either by returning a failure verdict or by throwing an error), the **entire routine fails** immediately. There is no `on_fail` edge for agent steps.
- **Gate Step Failure**: If a gate step fails (returns a failure verdict), execution follows the explicitly defined `on_fail` edge. This allows graceful degradation, bounded rework, escalation, or routing to a failure path.

This distinction ensures that critical work steps are treated as atomic (failure = routine failure), while gates can implement sophisticated error handling and branching.

## Common Workflow Patterns

Routines are typically built using well-known patterns:

- **Gated Pipeline** — Linear flow with validation gates at key stages
- **Fan Out** — Parallel independent work followed by a join/review
- **Linear Pipeline** — Simple sequential execution
- **Review Pipeline** — Generation → Review → Approval/Rejection path

## Graph Rules

- Routine graphs must be acyclic except bounded gate failure loops.
- Exactly one entry step should be present. Use `entry_steps` to name it.
- Every routine step has a stable `slug`. Edge `source_step`, edge
  `target_step`, and `entry_steps` should reference those step slugs.
- Every non-terminal step must have at least one outgoing edge.
- Terminal steps must not have outgoing edges.
- Every step must be reachable from the entry step.
- Every routine must have at least one reachable terminal step: `terminal` or
  `terminal_fail`.
- Edge `condition` values are `always`, `on_pass`, or `on_fail`.
- Gate outgoing edges must use `on_pass` and/or `on_fail`; do not use `always`
  from a gate.
- Agent and gate steps must set `agent` to the executor agent slug. Council
  steps must set `council` to the council slug. For scheduled routines, set the
  routine trigger to `cron`; the graph still uses ordinary routine nodes.
- If an `on_fail` edge creates a retry cycle, the source step must be a `gate`
  and the retry remains bounded by `metadata.max_attempts` or the runner default.
- Use `metadata.on_exhausted` to point to the step ref that should run when the
  retry edge exhausts its attempts.

## Bounded Rework Pattern

Express retries only as a bounded gate failure loop:

1. `implement -> review_gate`
2. `review_gate on_pass -> done`
3. `review_gate on_fail -> implement`, optionally with `metadata.max_attempts`
4. `metadata.on_exhausted -> failed` or an escalation step

The gate owns the retry decision. Agent steps do not use `on_fail` retry edges;
agent step failure fails the routine immediately.

## Runtime Behavior

When a routine is triggered:

1. The trigger (task or cron) provides initial context
2. Steps execute according to the graph edges
3. Gates evaluate output and decide the next path
4. Councils run multi-agent collaboration when reached
5. The routine ends at a `terminal` (success) or `terminal_fail` (failure)

Routines provide strong observability — every step transition, gate decision, and council output is recorded.

## Pitfalls to Avoid
- Creating overly complex graphs with too many steps
- Using gates without clear acceptance criteria
- Forgetting to define both success and failure terminal paths
- Creating cycles from non-gate steps, `on_pass` edges, or unbounded `on_fail`
  edges

## Best Practices
- Start simple (Linear Pipeline or Gated Pipeline)
- Use councils only when multiple perspectives or synthesis are genuinely needed
- Keep individual steps focused — move complex logic into abilities or sub-agents
