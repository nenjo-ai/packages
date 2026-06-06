# Tasks — Units of Project Work

## Purpose

Tasks are the smallest trackable units of work inside a Project. A task has a
title, description, acceptance criteria, status, priority, type, complexity, and
an execution surface. A task can run through either one assigned agent ref or one
routine ref, never both.

## Core Concepts

- Tasks represent discrete, trackable project work.
- Acceptance criteria define what "done" means and give gates something to
  evaluate.
- Dependencies are expressed between tasks in the same project.
- Execution uses the assigned agent or routine ref, plus current project and
  repository context.

## Lifecycle & Statuses

| Status | Description | User Settable? | Notes |
|---|---|---|---|
| `open` | Initial state after creation | Yes | Default |
| `backlog` | Waiting on unmet dependencies | Yes | Also used by scheduling logic |
| `ready` | Eligible for execution | Yes | Ready tasks can be dispatched |
| `assigned` | Claimed by a worker | No | Internal execution state |
| `in_progress` | Currently executing | No | Set by execution engine |
| `done` | Completed successfully | No | Terminal success |
| `failed` | Execution failed | No | Terminal failure with error context |

## Key Fields

- `title` — Required human-readable name.
- `description` — Optional detailed explanation of the work.
- `acceptance_criteria` — Optional completion contract used by gates.
- `status` — `open`, `backlog`, `ready`, `assigned`, `in_progress`, `done`, or
  `failed`.
- `priority` — `low`, `medium`, `high`, or `critical`.
- `type` — `bug`, `feature`, `task`, or `improvement`.
- `complexity` — Small 1-5 estimate used for planning and ordering.
- `order_index` — Secondary sort key within the same dependency level.
- `metadata` — Additional structured data.
- `assigned_agent` — Agent slug/ref used for direct agent execution.
- `routine` — Routine slug/ref used for routine execution.
- `required_tags` — Agent qualification tags used by scheduling and assignment
  policy.

## Assignment Rules

A task must have exactly one executor surface:

- assigned agent ref — Executes as a single agent run.
- routine ref — Executes through a routine graph.

Setting both is invalid. Updating one executor surface should clear the other.
When speaking to users, prefer slugs and human-readable refs over internal
storage identifiers.

## Tools

Use project-scoped tools for task work:

- `list_project_tasks`
- `get_project_task`
- `create_project_tasks`
- `update_project_task`
- `delete_project_task`

Use `list_project_execution_runs`, `get_project_execution_run`,
`start_project_execution`, `pause_project_execution`, and
`resume_project_execution` for execution lifecycle work. Do not confuse
execution runs with task definitions.

## Dependencies

Tasks can depend on other tasks in the same project.

Rules:

- A task cannot depend on itself.
- Dependencies must stay inside the same project.
- Cycles are invalid.
- A task becomes executable only after its dependency policy is satisfied.
- Execution order is determined by dependency order and then `order_index`.

## Agent Guidance

Reference this block when creating, triaging, updating, or explaining project
tasks. Read the project, relevant tasks, and referenced agent or routine before
writing changes.

## Pitfalls To Avoid

- Assigning both an agent and a routine.
- Creating executable tasks without acceptance criteria.
- Referring to internal storage identifiers instead of slugs/refs.
- Creating circular dependencies.
- Treating an execution run as the task itself.
