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
- What state must cross each edge for the next step to continue?
- How should joined branches preserve source-specific handoffs?

## Good Workflow Shape

A good workflow has:

- explicit inputs and outputs;
- clear task boundaries;
- dependency ordering;
- assignment rules;
- gate criteria where quality or risk matters;
- observable execution state;
- explicit handoff contracts between steps.

## Minimum Workflow Design

Include:

- whether the shape is a task set, routine, council, or combination;
- task dispatch and, when recurring work is needed, the task schedule;
- steps and their owners, including the `agent` slug for every agent or gate step;
- task instructions for each agent and gate step, written as
  `step.config.instructions`, that tell the assigned agent exactly what to do
  for that step;
- dependencies, parallelizable branches, and fan-in joins;
- explicit edge `purpose` and `handoff_instructions` on agent/gate source edges
  for the state each route must carry, plus `handoff_schema` for every agent or
  gate source edge;
- gate criteria and terminal outcomes;
- expected artifacts, Library evidence, or execution output at each handoff;
- retry, escalation, or human approval points;
- verification plan for graph structure and execution state.

A workflow can be implemented as a routine, council, agent, ability chain,
task set, or combination. Choose by the required guarantees:

| Need | Prefer |
|---|---|
| Deterministic step order, joins, retries, or explicit handoffs | routine |
| Fuzzy intent routing or adaptive planning | agent |
| Narrow reusable specialist operation | ability |
| Multiple perspectives, critique, voting, or synthesis | council |
| Trackable user/project work item | task |

Routines are started through task dispatch. A task may be manual or scheduled,
and its execution target may be the routine. Routines provide the
strongest audit trail for step results, gate decisions, fan-out, fan-in, and
bounded retry loops. Agents are more flexible and intent-driven, and can run in
chat, task, ability, or council contexts, but their intermediate control
flow is less graph-auditable unless they explicitly record it.

For platform routines, `entry_steps` may contain multiple step slugs; those
entry branches start in parallel. A step with multiple activated incoming edges
is an all-success join and runs only after every required upstream branch passes.
Multiple outgoing edges from an agent step are deterministic fan-out: the agent
must decompose the work so every downstream branch has a clear task. Use
councils when the workflow needs deliberative or agent-driven branch routing.

## Flow State Design

For routine-backed workflows, design the flow state before writing the graph.
Flow state is the runtime record of which entries, steps, edges, handoffs,
joins, gate decisions, retries, and terminals are active or complete.

Each edge leaving an agent or gate is an authored handoff contract. Use the
explicit edge field `purpose` to explain why the route exists and
`handoff_instructions` to tell the source agent what actual
information to pass through `route_next_steps`. The handoff should contain
source output, evidence, decisions, artifact refs, constraints, unresolved
questions, or required fixes. It should not merely restate the target step
instructions. Human outcome edges instead carry the scheduler's immutable
decision handoff.

Choose `handoff_schema` from the downstream step's minimum required
state, not from the source step's full output. Every edge whose source is an
`agent` or `gate` must use a JSON object schema. Prefer a small object with
named, required fields and `additionalProperties: false`. Use:

- `string` fields for concise findings, file paths, decisions, criteria, or
  notes the target must read. Artifact ID strings additionally require
  `format: nenjo-artifact-id`;
- arrays when the target needs a list of homogeneous items such as changed
  files, failed checks, risks, requirements, or evidence links;
- nested objects when one item has multiple required attributes, such as
  `{path, reason, status}`;
- `enum` or `const` for bounded states such as severity, verdict category, lane,
  approval state, or retry reason;
- booleans only when the target needs a true binary fact, and name the field so
  the meaning is unambiguous.

Do not choose a schema that is just `{work: string}` unless the downstream step
genuinely only needs one free-form work item. Do not use the schema to encode
instructions, optional commentary, or everything the source agent might know.
The schema is the enforceable payload shape; `handoff_instructions` explains how
to fill that shape well. These are top-level edge fields; do not wrap them in
edge `metadata`.

For fan-out, each downstream edge should request different handoff content. For
joins, the target step should state how to combine incoming handoff blocks while
keeping source steps distinct. A joined step should not treat upstream branches
as one blended transcript; it receives structured handoff blocks from activated
incoming edges.

For platform routines, keep ordinary flow acyclic. A `gate` step may use an
`on_fail` edge back to an earlier step for bounded rework. A human step may use
a reviewer-driven `changes_requested` edge back to an earlier step. Set
explicit edge `max_retries` only on a gate `on_fail` retry edge when its budget
should differ from the worker default of 3 rework traversals. The initial gate
evaluation is not a retry; `0` disables rework. Human review edges never use
this limit. When a gate retry edge exhausts its budget, the routine fails
directly with a structured `retry_exhausted` result; do not model exhaustion as
a separate branch.

A human step cannot be an entry step and must receive at least one incoming
handoff. Give it a `config.request.title` and at least one outgoing edge for
each fixed outcome: `approved`, `changes_requested`, and `rejected`. Incoming
edges must have unique source steps. An outcome may fan out, but those branches
must converge before one terminal result.

When writing a routine graph with platform tools, each step owner is explicit:
use `agent` for `agent` and `gate` steps, and `council` for `council` steps.
Agent and gate steps should also include task-specific instructions in
`config.instructions`. These instructions should describe the step's local
objective, inputs to inspect, output to produce, and any evidence or acceptance
standard the assigned agent must use. Do not rely on the routine name, step
slug, or the agent's general prompt to carry the step-specific work.
Keep step config typed and minimal. Agent and gate steps use `instructions` and
optional `metadata`, which the runtime includes in typed turn context; human
steps require `request`; terminal-fail steps may use `failure_reason`. Put
acceptance criteria and input references in the instruction text. Put retry
limits only on gate `on_fail` edge `max_retries`, not on the step config or
human outcome edges.
To run a routine periodically, schedule a task whose execution target is that
routine. A gate without an agent cannot execute because no model
has been assigned to evaluate the gate criteria and call `route_next_steps`.
Agent and gate steps call `route_next_steps`: this records the step verdict and
the auditable handoff payload for every activated downstream edge.
Terminal outcomes are also ordinary graph steps: add an explicit `terminal` or
`terminal_fail` step and point edges at that step slug.

## Common Patterns

- prompt chaining as a linear routine or a single structured agent;
- routing as a router agent, council, or explicit routine branch;
- parallelization as multiple entry steps, fan-out/fan-in, council members, or
  sub-agents;
- orchestrator-workers as planner/worker/synthesis steps, a council, or one
  orchestrator agent with abilities;
- evaluator-optimizer as a gate `on_fail` retry loop when audit matters;
- autonomous agent as a single agent, optionally targeted by a scheduled task;
- human approval as a human step with `approved`, `changes_requested`, and
  `rejected` outcomes; agent-evaluated approval as a gate.

## Task Dispatch And Scheduling

- A task supplies the initial work context for a routine execution.
- Recurrence belongs to the task schedule. The scheduled task targets either a
  routine or a single agent.

## Boundary Rules

- Use tasks for project-specific work units.
- Use routines for reusable execution graphs.
- Use councils for multi-agent judgment and synthesis.
- Keep capability-access changes on user-controlled platform surfaces.
- Keep agent prompts focused on behavior; put repeatable step order in routines.

## Agent Guidance

Use this when designing project work or routine structure. Read
`classification.workflow_patterns` to choose the implementation approach,
`building.workflow_pattern_cookbook` for concrete graph recipes,
`building.routine_flow_authoring` for explicit edge fields and handoff contracts, and
`resources.tasks`, `resources.routines`, and `resources.executions` for exact
mechanics.
