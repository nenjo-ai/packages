# Workflow Design

## Purpose
Use workflow design when deciding how tasks, routines, councils, gates, and executions should fit together.

Workflows should make dependencies, handoffs, review points, and terminal outcomes explicit.

## Design Questions

- Is this one task, a routine, or a council?
- Should the workflow shape be deterministic and audited, or flexible and
  intent-driven?
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
- task instructions for each agent and gate step, written as
  `step.config.instructions`, that tell the assigned agent exactly what to do
  for that step;
- dependencies, parallelizable branches, and fan-in joins;
- gate criteria and terminal outcomes;
- expected artifacts, Library evidence, or execution output at each handoff;
- retry, escalation, or human approval points;
- verification plan for graph structure and execution state.

A workflow can be implemented as a routine, council, agent, ability chain,
domain mode, task set, or combination. Choose by the required guarantees:

| Need | Prefer |
|---|---|
| Deterministic step order, joins, retries, or scheduled dispatch | routine |
| Fuzzy intent routing or adaptive planning | agent |
| Narrow reusable specialist operation | ability |
| Multiple perspectives, critique, voting, or synthesis | council |
| User-approved operating mode or permission boundary | domain |
| Trackable user/project work item | task |

Routines are coupled to task or cron scheduling and dispatch. They provide the
strongest audit trail for step results, gate decisions, fan-out, fan-in, and
bounded retry loops. Agents are more flexible and intent-driven, and can run in
chat, task, domain, ability, or council contexts, but their intermediate control
flow is less graph-auditable unless they explicitly record it.

For platform routines, `entry_steps` may contain multiple step slugs; those
entry branches start in parallel. A step with multiple activated incoming edges
is an all-success join and runs only after every required upstream branch passes.
Multiple outgoing edges from an agent step are deterministic fan-out: the agent
must decompose the work so every downstream branch has a clear task. Use
councils when the workflow needs deliberative or agent-driven branch routing.

For platform routines, keep ordinary flow acyclic. The valid retry shape is a
bounded gate failure loop: a `gate` step may use an `on_fail` edge back to an
earlier step. Set `metadata.max_attempts` when the retry budget should differ
from the runner default of 3 attempts. When the retry edge exhausts its budget,
the routine fails directly with a structured `retry_exhausted` result; do not
model exhaustion as a separate branch.

When writing a routine graph with platform tools, each step owner is explicit:
use `agent` for `agent` and `gate` steps, and `council` for `council` steps.
Agent and gate steps should also include task-specific instructions in
`config.instructions`. These instructions should describe the step's local
objective, inputs to inspect, output to produce, and any evidence or acceptance
standard the assigned agent must use. Do not rely on the routine name, step
slug, or the agent's general prompt to carry the step-specific work.
Keep step config typed and minimal: only `instructions` and optional `metadata`
are supported. Put acceptance criteria and input references in the instruction
text unless the prompt explicitly consumes `{{ routine.step.metadata }}`. Put
retry limits on `on_fail` edge `metadata.max_attempts`, not on the step config.
For scheduled routines, set the routine trigger to `cron`; the graph still uses
ordinary routine nodes. A gate without an agent cannot execute because no model
has been assigned to evaluate the gate criteria and call `pass_verdict`.
Agent steps call `route_next_steps` instead: this records the agent verdict and,
on pass, the auditable task decomposition for every downstream edge.
Terminal outcomes are also ordinary graph steps: add an explicit `terminal` or
`terminal_fail` step and point edges at that step slug.

## Common Patterns

- prompt chaining as a linear routine or a single structured agent;
- routing as a router agent, council, domain switch, or explicit routine branch;
- parallelization as multiple entry steps, fan-out/fan-in, council members, or
  sub-agents;
- orchestrator-workers as planner/worker/synthesis steps, a council, or one
  orchestrator agent with abilities;
- evaluator-optimizer as a gate `on_fail` retry loop when audit matters;
- autonomous agent as a single agent unless scheduling or checkpoints require a
  routine wrapper;
- approval or escalation as gates and explicit terminal outcomes.

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

Use this when designing project work or routine structure. Read
`classification.workflow_patterns` to choose the implementation approach,
`building.workflow_pattern_cookbook` for concrete graph recipes, and
`resources.tasks`, `resources.routines`, and `resources.executions` for exact
mechanics.
