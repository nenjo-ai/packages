# Agent Design

## Purpose
Use agent design when deciding what an agent should own, how broad its role should be, and which resources should be attached to it.

An agent is the right unit when behavior needs a durable identity, stable prompts, memory focus, model assignment, and a managed capability surface.

## Design Questions

- What job should this agent be trusted to perform repeatedly?
- What should stay in the agent prompt versus an ability, domain, routine, or knowledge pack?
- What memory focus does the agent need to remain useful over time?
- Which tools should be available by default, and which should require a domain or platform-controlled path?
- Which abilities, domains, context blocks, MCP servers, and knowledge packs are required at start?
- What model should be assigned, and why is that model appropriate for the expected work?

## Good Agent Shape

A good agent has:

- one clear responsibility;
- a stable behavioral identity;
- explicit prompt boundaries;
- a narrow default capability set;
- enough knowledge access to answer its domain questions;
- memory guidance that matches its expected lifetime.
- reuses available context blocks appropriately based on the descrtiption and contents

## Design Patterns

| Pattern | Use When |
|---|---|
| Generalist builder | The agent coordinates many resource types and uses search before acting |
| Specialist reviewer | The agent evaluates a narrow artifact type or risk class |
| Operator | The agent performs repeated operational checks or maintenance |
| Council leader | The agent delegates work and synthesizes member outputs |

## Minimum Agent Design

Include:

- display name and user-facing description;
- durable responsibility and non-goals;
- system prompt purpose, principles, and behavioral boundaries;
- developer guidance for tool use, retrieval, verification, and escalation;
- model slug or model requirements;
- memory profile focus areas;
- runtime templates only when chat, routine task, gate, or heartbeat behavior differs;

## Boundary Rules

- Use an agent when the behavior needs identity, memory, model choice, and
  recurring responsibility.
- Use an ability for a narrow operation the parent agent can call.
- Use a domain when the user must explicitly activate a different mode or added
  capabilities.
- Use a routine when the main complexity is ordered steps, gates, and
  execution state.
- Use a council when independent perspectives and final synthesis matter.

## Pitfalls

- Combining unrelated jobs into one broad agent.
- Encoding one-off task context into the core prompt.
- Giving the agent too many default tools.
- Using an agent when an ability or routine would be simpler.
- Designing assignments vaguely instead of naming the complete intended ability,
  domain, model, MCP, context, and knowledge surface.
- Optimization the agent prompts early on. Start simple and add complexity later when the work demands it.

## Checklist
Complete the following checklist to ensure a good agent shape  
- well defined system prompt
- well defined developer prompt?
- well defined memory profile
- well defined chat, task, gate, and heartbeat task templates
- prompts and task templates resuse the context blocks

## Agent Guidance

Read this before creating or redesigning an agent. Then read `resources.agents` for exact resource behavior and `building.prompt_structuring` for prompt composition.
