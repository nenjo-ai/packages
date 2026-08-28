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
6. Platform tools are rebuilt from the ability's own `platform_scopes`; the
   ability does not inherit the caller's platform write authority.
7. Host tools remain capability-scoped to the worker workspace.
8. It returns control to the caller with a focused result.

The caller should pass stable artifact IDs or catalog paths in the ability task
description when specialist work depends on an artifact. A nested ability does
not implicitly receive the caller turn's media attachments.

## Tools

Use these platform tools for ability work:

- `list_abilities`
- `get_ability`
- `configure_ability`

Use `list_abilities` to discover existing names/slugs and `get_ability` to
inspect the full AbilityDocument, including prompt_config and tool assignments.
Use `configure_ability` for authored ability writes. Always provide the stable
top-level `slug`; if it does not exist, also provide `name` and `prompt_config`.
Omitted fields remain unchanged and a supplied `mcp_servers` array is a complete
replacement. Do not use `metadata`, `assignments`, or a separate `ability`
selector. Script-tool and platform-scope mutation are outside this tool. A
successful response contains the same canonical AbilityDocument as
`get_ability`.

## Pitfalls To Avoid

- Using abilities for long-running ownership; use an agent instead.
- Using abilities for explicit user modes; use a domain instead.
- Hiding broad privilege expansion behind an ability.
- Assuming the caller's platform scopes or attached media are implicitly
  inherited by the nested ability.
- Creating many overlapping abilities instead of one clear specialist contract.
