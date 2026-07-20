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
- memory guidance that matches its expected lifetime;
- reusable context blocks selected by role need, not by availability.

## Knowledge And Context Selection

Seeded knowledge belongs in an agent prompt only when that knowledge pertains to
the agent's recurring role, decisions, or expected source material. Do not seed
broad packs or docs just because they are available. If the agent only
occasionally needs a source, give it retrieval guidance and the right knowledge
tools instead of injecting the source index into every turn.

Use context blocks when they directly support how the agent should operate.
Knowledge docs describe selector names as literal references; they do not render
context blocks themselves.

| Agent Need | Useful Context Selector |
|---|---|
| Finds and reads Library/package docs | `pkg.nenjo_ai.packages.context.knowledge.knowledge_routing` |
| Uses filesystem, repository, or shell tools | `pkg.nenjo_ai.packages.context.tools.host_tools` |
| Performs platform writes | `pkg.nenjo_ai.packages.context.operations.write_discipline` |
| Diagnoses failures or recovery paths | `pkg.nenjo_ai.packages.context.operations.failure_modes` |
| Delegates to sub-agents | `pkg.nenjo_ai.packages.context.agents.sub_agents` |
| Delegates to installed agents | `pkg.nenjo_ai.packages.context.agents.agent_delegation` |
| Orchestrates abilities | `pkg.nenjo_ai.packages.context.agents.ability_orchestration` |
| Depends on durable memory behavior | `pkg.nenjo_ai.packages.context.memory.remembrance` |
| Runs tasks, gates, or chat wrappers | the matching `pkg.nenjo_ai.packages.context.runtime.*` selector |

Do not add context selectors that do not support the role. Read context block
metadata and content to choose useful selectors, then include the selectors
rather than copying resolved templates.

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
- role-relevant seeded knowledge selectors, if any;
- role-relevant context block selectors, if any;
- model slug or model requirements;
- memory profile focus areas;
- runtime templates only when chat, task, or gate behavior differs;

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
- well defined chat, task, and gate templates
- seeded knowledge is limited to sources that pertain to the agent's role
- prompts and task templates reuse selected context blocks by selector

## Agent Guidance

Read this before creating or redesigning an agent. Then read `resources.agents` for exact resource behavior and `building.prompt_structuring` for prompt composition.
