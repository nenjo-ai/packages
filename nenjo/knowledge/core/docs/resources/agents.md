# Agents — Primary Behavioral Units

## Purpose

Agents are the primary behavioral units of Nenjo. An agent defines how work is
interpreted, what role it performs, what prompt context matters, and which
capabilities it can access.

## What An Agent Owns

An agent owns its behavior and runtime surface:

- behavioral identity and description;
- prompt configuration;
- memory profile;
- model assignment;
- assigned abilities;
- assigned domains;
- assigned MCP servers;
- assigned script tools;
- optional heartbeat schedule.

Platform scopes are attached to agents as permission refs, but agents must not
grant or change their own scopes. Scope assignment is controlled by users and
platform policy.

## Runtime Modes

| Mode | Description | Primary Template |
|---|---|---|
| chat | User conversation | `templates.chat` |
| task | Routine agent-step execution | `templates.task` |
| gate | Evaluation of prior output | `templates.gate` |
| heartbeat | Periodic autonomous check | `templates.heartbeat` |
| council | Council member or leader work | `templates.task` adapted by the runtime |

The agent's identity and memory profile remain stable across modes. The runtime
mode changes the template and injected variables.

Runtime templates should provide mode-specific input, not define a different
public voice for each mode. Stable response style, hidden intent
classification, and routing discipline belong in the developer prompt or a
reusable context block.

For generic behavior, prefer one-line runtime templates that include reusable
runtime context blocks, for example
`{{ pkg.nenjo_ai.packages.context.runtime.task_execution }}`. Put task, project,
metadata, routine, gate, or heartbeat variable framing inside that context block
instead of duplicating it across agent templates.

## Prompt Configuration

Prompt content is treated as a subresource. Metadata updates and prompt updates
should be handled separately when the platform exposes separate tools.

- `system_prompt` — stable identity and principles.
- `developer_prompt` — tactical behavior, routing, and tool-use guidance.
- `templates.chat` — chat wrapper.
- `templates.task` — routine agent-step execution wrapper.
- `templates.gate` — gate evaluation wrapper.
- `templates.heartbeat` — heartbeat wrapper.

## Assigned Capability Surface

Agents gain capabilities through explicit assignments:

- Abilities — callable specialist behaviors.
- Domains — user-activated execution modes.
- MCP servers — external tool integrations.
- Script tools — package or native script-backed tools.

Use the minimum surface needed for the job. Agents can recommend needed
capabilities, but permission and scope changes must be performed by users or
platform-controlled setup.

## Tools

Use these platform tools for agent work:

- `list_agents`
- `get_agent`
- `configure_agent`

Use `list_agents` to discover existing slugs and `get_agent` to inspect the full
AgentDocument. Use `configure_agent` for all agent writes: creation, metadata
patches, prompt_config patches, and full replacement ability/domain/MCP server
assignment lists. Omit `agent` to create a new agent; include the existing agent
slug to update one.

## Pitfalls To Avoid

- Broadening a base prompt instead of assigning a focused ability.
- Splitting one agent change across legacy create/update/prompt tools.
- Asking agents to assign platform scopes.
- Reporting internal storage identifiers when slugs/refs are sufficient.
