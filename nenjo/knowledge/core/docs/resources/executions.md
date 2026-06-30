# Executions

## Purpose

Executions are stateful runs that process project tasks, chat turns, scheduled
work, heartbeats, and domain-expanded sessions through the runtime.

An execution is the runtime container for coordinated processing, cancellation,
pausing, transcript recording, checkpoints, and trace state. It is not the task
definition itself.

## Core Model

An execution usually belongs to a project and controls:

- which tasks are linked into the run
- how many tasks can run in parallel
- the current lifecycle state of the run
- event and progress tracking for the run
- active cancellation and pause handles
- session transcript, checkpoint, and trace updates
- routine flow state when the execution is running a routine

## Lifecycle

- create in `pending`
- start as a separate command
- dispatch ready work up to `parallel_count`
- re-evaluate dependents as tasks finish
- complete when no runnable work remains

Platform harness handlers track active executions by cancellation key and
execution kind. The shared execution kinds are `chat`, `task`, `cron`, and
`heartbeat`. Domain sessions keep a domain-expanded runner active until the
domain exits or the session ends.

Session runtime state is recorded through `nenjo-sessions` contracts. Worker
storage may clean up terminal file-backed sessions after the configured
retention period.

## Routine Flow State

Routine executions add graph-level state on top of the execution lifecycle.
The execution can be `running` while individual routine steps are pending,
blocked on joins, running, passed, failed, skipped by routing, or terminal.

Routine flow state includes:

- active entry steps from `entry_steps`;
- step status and recorded step output;
- activated outgoing edges;
- `route_next_steps` verdicts and per-target handoffs from agent and gate steps;
- join readiness for steps with multiple activated incoming edges;
- retry attempt counts for gate `on_fail` loops;
- terminal, terminal_fail, agent failure, or retry-exhausted completion state.

For fan-out, the execution records which downstream edges were activated and
the handoff provided for each target. For joins, the target step becomes
runnable only after every required activated upstream branch has passed and
provided its result or handoff.

When debugging a routine run, inspect both lifecycle state and graph state:
an execution may still be healthy while a join is waiting, a gate retry is
pending, or a branch has not yet produced its handoff. A routine that fails
because an agent step returned a fail verdict or a retry edge exhausted its
budget is terminal even if the authored graph has a `terminal_fail` step that
was not reached.

## Statuses

- `pending`
- `running`
- `paused`
- `cancelled`
- `completed`

Routine graph step statuses may be more granular than these execution statuses.
Use execution status for the run lifecycle and routine flow state for step,
edge, join, handoff, and retry reasoning.
