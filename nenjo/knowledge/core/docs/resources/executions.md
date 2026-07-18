# Executions

## Purpose

An execution is one invocation of a task. Manual dispatch, retry, and scheduled
activation all create task-scoped execution runs with the same lifecycle and
history model. The execution is runtime state; it is not the reusable task
definition.

## Core Model

An execution run records:

- the task and optional project snapshot;
- the selected agent or routine target;
- trigger `manual`, `retry`, or `schedule`;
- schedule occurrence identity when applicable;
- parent run for retry lineage;
- dispatch, queue, start, completion, and failure timestamps;
- canonical execution events and final output attachments. Text output is kept
  in the database; non-text output is stored in object storage.

A task may have only one active run. Project-level orchestration may make
several project tasks eligible, but each invocation still has its own task run
and enters the worker inbox independently.

## Lifecycle

| State | Meaning |
|---|---|
| `pending` | Run created but not yet durably accepted by a worker |
| `queued` | Persisted in the worker inbox and waiting for concurrency |
| `running` | Holding a worker concurrency permit and executing |
| `paused` | Execution is paused where supported |
| `completed` | Canonical task completion and outputs were persisted |
| `failed` | Execution terminated with an error |
| `cancelled` | Execution was intentionally stopped |

The worker reports inbox states such as queued and running. Canonical task
completion events finalize successful execution and attachments; an
observational inbox completion must not finalize the run before outputs arrive.
The dashboard therefore treats terminal worker state as progress until the
canonical completion event has persisted the task run's outputs.

## Manual, Retry, And Scheduled Runs

- Manual execution dispatches the selected task immediately.
- Retry creates a new run linked to the previous run and republishes the
  retained task command.
- A schedule occurrence uses a deterministic schedule/occurrence identity so
  restart or redelivery remains idempotent.

All three paths use the harness-owned durable task inbox and the same worker
concurrency limit. There is no separate execution kind or session lifecycle for
scheduled work.

## Agent Observation Flow

`dispatch_task` and `retry_execution_run` return an `execution_run_id`
immediately. For a routine-targeted task, call `watch_execution_run` once with
that ID. It starts a model-visible async operation and returns its operation ID
plus initial routine progress. Step updates record completed, active, and
remaining work without waking the agent; completion or failure wakes the agent.
Use `wait_operations` when you need to inspect progress. The watcher has no
cursor and is not used for direct-agent runs.

Use `list_task_execution_runs` with `activity: active` to find all pending,
queued, running, or paused work. Filter by `task_slug`, project, agent, or
routine when narrowing the result. This is the same execution surface for
manual and scheduled tasks, including routine-targeted tasks.

The list tool returns a compact execution summary: run `id`, `task_slug`,
lifecycle `status`, trigger, target slug, relevant timestamps, and an error only
when present. Scheduled triggers include their `scheduled_for` occurrence time.
Platform command snapshots, encrypted payloads, organization and resource
UUIDs, and bookkeeping fields are not agent-facing. Dispatch, retry, and cancel
return only the `execution_run_id` and accepted status.

## Routine Flow State

When a task targets a routine, its execution adds graph-level state:

- activated entry steps;
- step status and output;
- activated edges and per-target handoffs;
- join readiness;
- gate verdicts and bounded retry attempts;
- terminal, terminal-fail, agent-failure, or retry-exhausted outcome.

For fan-out, the run records which downstream edges were activated and the
handoff supplied for each target. A join becomes runnable only after every
required activated upstream branch passes and provides its handoff.

When debugging a routine-targeted task, inspect both task-run lifecycle and
routine flow state. A run can be healthy while a join is waiting or a bounded
gate retry is pending.

## Guidance

- Use the task as the durable definition and the execution as one invocation.
- Treat manual, retry, and schedule triggers as origins of the same lifecycle.
- Do not create resource-specific scheduled sessions.
- Do not mark a run completed before canonical outputs are persisted.
- Use task history and parent-run linkage when explaining retries.
