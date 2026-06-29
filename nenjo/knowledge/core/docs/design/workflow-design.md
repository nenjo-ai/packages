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
- trigger or starting condition;
- steps and their owners, including the `agent` slug for every agent or gate step;
- task instructions for each agent and gate step, written as
  `step.config.instructions`, that tell the assigned agent exactly what to do
  for that step;
- dependencies, parallelizable branches, and fan-in joins;
- edge metadata with `metadata.purpose` and
  `metadata.handoff_instructions` for the state each route must carry, plus
  `metadata.handoff_schema` for every agent or gate source edge;
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

## Flow State Design

For routine-backed workflows, design the flow state before writing the graph.
Flow state is the runtime record of which entries, steps, edges, handoffs,
joins, gate decisions, retries, and terminals are active or complete.

Each edge is a handoff contract. Use `metadata.purpose` to explain why the
route exists and `metadata.handoff_instructions` to tell the source agent what
actual information to pass through `route_next_steps`. The handoff should
contain source output, evidence, decisions, artifact refs, constraints,
unresolved questions, or required fixes. It should not merely restate the target
step instructions.

Choose `metadata.handoff_schema` from the downstream step's minimum required
state, not from the source step's full output. Every edge whose source is an
`agent` or `gate` must use a JSON object schema. Prefer a small object with
named, required fields and `additionalProperties: false`. Use:

- `string` fields for concise findings, artifact refs, file paths, decisions,
  criteria, or notes the target must read;
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
to fill that shape well.

For fan-out, each downstream edge should request different handoff content. For
joins, the target step should state how to combine incoming handoff blocks while
keeping source steps distinct. A joined step should not treat upstream branches
as one blended transcript; it receives structured handoff blocks from activated
incoming edges.

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
has been assigned to evaluate the gate criteria and call `route_next_steps`.
Agent and gate steps call `route_next_steps`: this records the step verdict and
the auditable handoff payload for every activated downstream edge.
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
`building.workflow_pattern_cookbook` for concrete graph recipes,
`building.routine_flow_authoring` for edge metadata and handoff contracts, and
`resources.tasks`, `resources.routines`, and `resources.executions` for exact
mechanics.
