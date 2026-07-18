# Tasks — Executable Organization Work

## Purpose

Tasks are the first-class units of executable work in Nenjo. Every task belongs
to an organization and may optionally belong to a project. A project supplies
project context and its dedicated workspace; a task without a project runs in
the worker's main workspace.

A task can target one agent or one routine. Manual dispatch, retry, and
scheduled activation all use the same task definition, execution history,
worker inbox, and concurrency limit.

## Core Model

- Tasks are organization resources; project membership is optional.
- Every task requires `instructions` containing the work to perform. Sensitive instructions are
  stored and transported as task-bound encrypted content.
- A task may have one execution target: an agent or routine. Platform storage
  uses UUID foreign keys; agent-facing tools use resource slugs.
- Organization-owned statuses and labels replace fixed status strings and tag
  arrays.
- Recurrence is owned by the task schedule, never by an agent or routine.

## Key Fields

- `id` — Stable task UUID.
- `task_number` and `identifier` — Organization-local display identity such as
  `T-42`.
- `project_id` — Optional project association.
- `title` — Required human-readable summary.
- `instructions` — Required, non-empty execution instructions.
- `status` / `status_id` — Organization-owned workflow status.
- `priority` — `low`, `medium`, `high`, or `critical`.
- `assignee_user_id` — Optional human assignee.
- `execution_target` — Optional agent or routine target.
- `labels` / `label_ids` — Organization-owned task labels.
- `slug` — Optional stable human-readable reference.
- `metadata` — Additional non-secret structured data.
- `completed_at` — Completion timestamp when the task reaches a completed
  status.

The retired task shape must not be used. Do not send `description`,
`acceptance_criteria`, `tags`, `required_tags`, `type`, `complexity`, or
`order_index` as task fields. Fold completion guidance and other task-specific
requirements into `instructions`, and use labels for classification.

## Workflow Statuses

Statuses are organization-owned rows with stable categories:

| Category | Meaning |
|---|---|
| `backlog` | Planned work that is not yet runnable |
| `unstarted` | Ready work (for example, a Todo status) that may be dispatched |
| `started` | Work is actively in progress |
| `completed` | Work completed successfully |
| `canceled` | Work was intentionally stopped |

Display names, colors, and ordering are customizable. Use the status UUID from
the organization rather than inventing fixed status strings.

## Execution Targets

A task may be saved without an execution target while it is being planned, but
execution requires exactly one target. Agent-facing tools use slug references:

```json
{"type":"agent","slug":"code-generator"}
```

or:

```json
{"type":"routine","slug":"code-generation-pipeline"}
```

Setting both is invalid. An agent-targeted task runs the agent directly. A
routine-targeted task starts the routine graph with the task as its initial
context.

## Schedules

A task may own at most one schedule. Its calendar definition records a start
time, IANA timezone, recurrence, and optional end condition. Recurrence may be
an interval, daily, weekly, monthly, yearly, or an advanced cron expression.
Schedules may be enabled or paused; overlap is skipped and missed occurrences
run once before advancing. Every occurrence creates a task execution run.

Every worker receives the schedule snapshot, while only the worker holding the
exclusive scheduling capability activates due occurrences. Scheduled and
manual tasks enter the same durable worker inbox and count toward the same
concurrency limit.

To schedule recurring agent behavior, create a task targeting that agent and
put the recurring instructions on the task. To schedule a routine, create a
task targeting the routine and schedule the task.

## Tools

Agents with task scopes use task-centered tools:

- `list_tasks` returns compact summaries using task, project, agent, and routine
  slugs plus status and label names. It intentionally omits instructions and
  timestamps; use `get_task` when those details are needed;
- `get_task` returns the detailed task document, including decrypted
  instructions and timestamps, without platform UUID or catalog bookkeeping;
- `list_task_labels` returns the organization label catalog. Reuse its names
  when possible;
- `configure_task` creates a definition when `task_slug` is omitted and updates
  one when `task_slug` is supplied. It accepts project, agent, and routine slugs,
  label names, and an existing workflow status name. Missing labels are created
  with a new task, while updates require existing label names. Its input is a
  partial patch and its output is the full saved task document;
- `dispatch_task` starts a manual run by `task_slug` and returns
  `execution_run_id`;
- `list_task_execution_runs` lists history or active runs;
- `watch_execution_run` starts one async progress operation for a routine run.
  Step updates do not wake the agent; completion or failure does;
- `cancel_execution_run` requests cancellation of queued or running work;
- `retry_execution_run` creates a new run from a terminal run;
- `delete_task` permanently removes a task, its runs, and attachments by slug.

Task scheduling is configured through the task schedule surface; it is not an
alternate routine or agent configuration.

## Pitfalls To Avoid

- Treating every task as project-owned.
- Confusing project patch cases: a project slug assigns, an omitted `project`
  preserves the current association, and `project: null` removes it. Null never
  assigns a project.
- Retrying an identical successful `configure_task` call when the returned
  document does not match the intent. Compare the actual submitted arguments
  with the returned task first.
- Giving a task both an agent and routine target.
- Assuming an agent target implicitly enters a routine where that agent happens
  to be the first step. The routine itself must be the task target.
- Putting timer state on an agent or routine.
- Using retired task fields instead of `instructions`, labels, and status rows.
- Dispatching a backlog task before moving it to a runnable workflow status.
- Treating scheduled occurrences as a separate execution model.
