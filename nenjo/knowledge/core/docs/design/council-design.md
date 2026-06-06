# Council Design

## Purpose
Use council design when a problem benefits from multiple agent perspectives, delegation, review, or synthesis.

Councils should be reserved for work where collaboration adds value. They are heavier than a single agent or ability call.

## Design Questions

- What decision or artifact requires multiple perspectives?
- Who leads, who contributes, and who reviews?
- Should members work in sequence, in parallel, or behind gates?
- What final synthesis is expected?

## Good Council Shape

A good council has:

- a clear leader;
- distinct member roles;
- explicit delegation strategy;
- bounded output expectations;
- a synthesis step that resolves disagreement.

## Minimum Council Design

Include:

- leader agent and responsibility;
- member agents and distinct perspectives;
- when members run in parallel versus sequence;
- input artifact or decision question;
- expected member outputs;
- synthesis criteria and final owner;
- escalation or disagreement handling;
- verification plan for delegation, completion, and final synthesis quality.

## Use A Council For

- architecture review;
- policy review;
- high-risk planning;
- multi-domain analysis;
- workflows where independent perspectives reduce blind spots.

## Pitfalls

- Using a council for simple single-agent work.
- Assigning overlapping member roles.
- Failing to specify who produces the final answer.
- Treating parallel opinions as enough without a clear synthesis step.

## Boundary Rules

- Use a council when independent perspectives reduce risk or improve quality.
- Use a routine when the important structure is deterministic step order.
- Use an ability when a single narrow specialist can answer.
- Use a single agent when collaboration does not add meaningful evidence.

## Agent Guidance

Use this before recommending or building a council. Read `resources.councils` for structure and `classification.workflow_patterns` for orchestration options.
