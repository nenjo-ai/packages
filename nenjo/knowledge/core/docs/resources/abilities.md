# Abilities — Reusable Specialist Execution Modes

## Purpose

Abilities are reusable specialist execution modes exposed to an agent as callable
tools. They give an agent a narrow, high-signal behavior without turning that
behavior into a standalone agent, domain, or routine.

## What An Ability Is

An ability:

- Is assigned to one or more agents or domains.
- Appears at runtime under its `tool_name`.
- Has an `activation_condition` that tells the caller when to use it.
- Runs as a nested specialist execution under the calling agent.

Abilities are the right unit for specialization, not long-running ownership.

## Core Fields

- `name` — Stable internal name.
- `tool_name` — Callable tool name presented to the agent.
- `display_name` — Human-readable name.
- `description` — What the ability does.
- `activation_condition` — When the caller should use it.
- `prompt_config.developer_prompt` — Specialist guidance for the nested run.
- `mcp_servers` — MCP server slugs/refs available to the ability.
- `script_tools` — Script tool slugs/refs available to the ability.
- `platform_scopes` — Platform permission refs. Agents must not grant or change
  these; users or platform-controlled setup must assign them.

## Runtime Behavior

When an ability is invoked:

1. It runs as a nested execution under the caller.
2. It inherits the caller's task, project context, user request, and memory
   surface.
3. It keeps the caller's system framing.
4. It replaces developer guidance with its own specialist prompt.
5. It receives its configured tool refs.
6. It returns control to the caller with a focused result.

## Tools

Use these platform tools for ability work:

- `list_abilities`
- `get_ability`
- `configure_ability`

Use `list_abilities` to discover existing names/slugs and `get_ability` to
inspect the full AbilityDocument, including prompt_config and tool assignments.
Use `configure_ability` for all ability writes: creation, metadata changes,
prompt_config changes, and full replacement MCP/script tool assignments. Do not
use `configure_ability` to assign platform scopes. Scope changes are a
user/platform operation.

## Pitfalls To Avoid

- Using abilities for long-running ownership; use an agent instead.
- Using abilities for explicit user modes; use a domain instead.
- Hiding broad privilege expansion behind an ability.
- Creating many overlapping abilities instead of one clear specialist contract.
