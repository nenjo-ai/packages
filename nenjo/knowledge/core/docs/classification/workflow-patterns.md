# Workflow Pattern Classification

## Purpose

Classify common AI workflow patterns and map them to Nenjo implementation
choices. Use this before building routines, councils, agents, or task plans.

A workflow pattern can often be implemented in several ways. Choose by the
needed balance of auditability, determinism, flexibility, scheduling, failure
semantics, parallelism, and cost.

## Implementation Tradeoffs

| Axis | Routine | Agent / Ability / Council |
|---|---|---|
| Auditability | Strong step, edge, gate, and retry trail | Weaker unless the agent records evidence explicitly |
| Determinism | Graph controls order, dependencies, and joins | Model decides flow dynamically |
| Flexibility | Lower because topology is authored up front | Higher because intent and context can shape execution |
| Scheduling | Coupled to task or cron dispatch | Can run in chat, task, ability, council, or domain contexts |
| Failure semantics | Explicit terminal, terminal_fail, gate retry exhaustion | Prompt and tool dependent |
| Parallelism | Explicit entry steps, fan-out edges, and joins | Possible through sub-agents or council members, less graph-visible |
| Cost/control | Predictable shape | More variable but often simpler for exploratory work |

Use routines when the workflow shape is part of the product contract. Use
agents, abilities, or councils when the workflow shape is part of the reasoning.

## Core Patterns

| Pattern | Intent | Nenjo Implementations | Default Guidance |
|---|---|---|---|
| Prompt chaining | Run ordered transformations or analyses | Linear routine; one agent with structured phases | Use a routine when phases need audit, different owners, gates, or model choices |
| Routing | Choose one path from task intent, context, or evidence | Router agent; council; routine branches; domain switch | Use an agent for fuzzy intent. Use a routine when route choice is an audited business process |
| Parallelization | Run independent branches and combine results | Multiple `entry_steps`; agent fan-out plus join; council members; sub-agents | Use a routine for auditable branch state. Use a council for judgment. Use sub-agents for flexible speed |
| Orchestrator-workers | Decompose work, assign specialists, synthesize | Planner step -> worker branches -> synthesis; council leader and members; single agent delegation | Use a routine when worker lanes are predictable. Use an agent or council when decomposition is dynamic |
| Evaluator-optimizer | Generate, evaluate, revise within a bounded budget | Gate `on_fail` retry loop with `max_attempts`; reviewer ability; self-reflection agent loop | Use a routine when retry count, evidence, and outcomes must be recorded |
| Autonomous agent | Let one agent pursue a goal with tools over time | Single agent with abilities/domains/tools; scheduled routine wrapper when needed | Use an agent by default. Wrap in a routine only for scheduling, audit checkpoints, or deterministic handoffs |
| Human/tool approval | Pause or gate progress on external approval or evidence | Gate step; explicit terminal_fail/escalation path; agent asks user/tool | Use a routine when approval is a workflow checkpoint |
| Adversarial review | Challenge an answer before acceptance | Council with critic/reviewer roles; routine generate -> critique -> gate | Use a council for contested judgment. Use a routine when the challenge path must be explicit |

## Decision Framework

1. Start with a single agent if the work is fuzzy, conversational, or
   intent-driven.
2. Add an ability when a narrow reusable specialist operation is enough.
3. Use a routine when step order, audit trail, retry budget, or scheduled
   dispatch matters.
4. Use a council when independent perspectives, voting, critique, or synthesis
   add real signal.
5. Combine routine and council when the macro workflow is deterministic but one
   step needs collaborative judgment.
6. Keep routine ordinary flow acyclic. The only valid cycle is a bounded gate
   `on_fail` retry loop. Retry exhaustion fails the routine directly.

## Agent Guidance

After choosing a pattern, read `design.workflows` for resource boundaries and
`building.workflow_pattern_cookbook` for concrete Nenjo graph recipes.
