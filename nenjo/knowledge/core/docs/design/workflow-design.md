# Workflow Design

## Purpose
Use workflow design when deciding how tasks, routines, councils, gates, and executions should fit together.

Workflows should make dependencies, handoffs, review points, and terminal outcomes explicit.

## Design Questions

- Is this one task, a routine, or a council?
- Which steps can run independently?
- Where are gates or reviews required?
- What evidence determines completion?
- Which agent owns each step?

## Good Workflow Shape

A good workflow has:

- explicit inputs and outputs;
- clear task boundaries;
- dependency ordering;
- assignment rules;
- gate criteria where quality or risk matters;
- observable execution state.

## Minimum Workflow Design

Include:

- whether the shape is a task set, routine, council, or combination;
- trigger or starting condition;
- steps and their owners, including the `agent` slug for every agent or gate step;
- dependencies and parallelizable branches;
- gate criteria and terminal outcomes;
- expected artifacts, Library evidence, or execution output at each handoff;
- retry, escalation, or human approval points;
- verification plan for graph structure and execution state.

For platform routines, keep ordinary flow acyclic. The valid retry shape is a
bounded gate failure loop: a `gate` step may use an `on_fail` edge back to an
earlier step. Set `metadata.max_attempts` when the retry budget should differ
from the runner default of 3 attempts. Use `metadata.on_exhausted` to route
exhausted retries to a terminal failure or escalation step.

When writing a routine graph with platform tools, each step owner is explicit:
use `agent` for `agent` and `gate` steps, and `council` for `council` steps.
For scheduled routines, set the routine trigger to `cron`; the graph still uses
ordinary routine nodes. A gate without an agent cannot execute because no model
has been assigned to evaluate the gate criteria and call `pass_verdict`.

## Common Patterns

- linear pipeline;
- gated review with explicit pass/fail terminal or escalation paths;
- fan-out and synthesis;
- council deliberation;
- recurring routine;
- escalation path.

## Routine Trigger Choice

- Use `task` when a project task should start the workflow and provide the
  initial work context.
- Use `cron` when the routine itself owns a schedule and should run
  periodically.

## Boundary Rules

- Use tasks for project-specific work units.
- Use routines for reusable execution graphs.
- Use councils for multi-agent judgment and synthesis.
- Use domains for user-approved modes that change capability access.
- Keep agent prompts focused on behavior; put repeatable step order in routines.

## Agent Guidance

Use this when designing project work or routine structure. Read `resources.tasks`, `resources.routines`, `resources.executions`, and `classification.workflow_patterns` for exact mechanics.
