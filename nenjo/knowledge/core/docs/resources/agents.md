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
- assigned MCP servers;
- assigned script tools.

Agents do not own schedules. Recurring agent work is represented by an ordinary
task whose execution target is the agent and whose schedule is owned by that
task.

Platform scopes are attached to agents as permission refs, but agents must not
grant or change their own scopes. Scope assignment is controlled by users and
platform policy.

## Runtime Modes

Every execution begins with session control and optional session data. Each
logical turn then receives turn control, optional turn data, and raw input. Empty
data messages are omitted.

| Mode | Raw input after typed context |
|---|---|
| chat | User message |
| task | Task instructions |
| gate | Prior result/evaluation input |
| council | Member or leader instructions through the underlying chat/task run |

The runtime supplies agent identity in session control and owns mode-specific
session and turn context. Stable response style, hidden intent classification,
and routing discipline belong in the developer prompt or a reusable static
context block. The memory profile configures retrieval; the resulting frozen
snapshot is session data.

## Prompt Configuration

Prompt content is treated as a subresource. Metadata updates and prompt updates
should be handled separately when the platform exposes separate tools.

- `system_prompt` — optional invariants inherited by every nested ability; omit
  it when no such invariant is needed.
- `developer_prompt` — role behavior, routing, and tool-use guidance.
- `memory_profile` — retrieval and memory-writing focus; runtime configuration,
  not model-visible prompt text.

Runtime input serialization is not part of prompt configuration. Agent identity
is also not authored there because session control supplies the manifest
identity and description.

## Assigned Capability Surface

Agents gain capabilities through explicit assignments:

- Abilities — callable specialist behaviors.
- MCP servers — external tool integrations.
- Script tools — package or native script-backed tools.

Use the minimum surface needed for the job. Agents can recommend needed
capabilities, but permission and scope changes must be performed by users or
platform-controlled setup.

Chat and task requests may include typed immutable artifact inputs. The runtime
routes supported media to the agent's chat model or to an organization
capability default. Artifact catalog tools are exposed by platform scope policy;
they are not prompt variables or ordinary filesystem paths.

## Tools

Use these platform tools for agent work:

- `list_agents`
- `get_agent`
- `configure_agent`

Use `list_agents` to discover existing slugs and `get_agent` to inspect the full
AgentDocument. Use `configure_agent` for all agent writes. Always provide the
stable top-level `slug`; if it does not exist, also provide `name`. Omitted
fields remain unchanged, nullable fields accept `null` to clear, and supplied
ability/MCP server arrays are complete replacements. Do not use
`metadata`, `assignments`, or a separate `agent` selector. A successful response
contains the same canonical AgentDocument as `get_agent`.

## Pitfalls To Avoid

- Broadening a base prompt instead of assigning a focused ability.
- Splitting one agent change across multiple calls instead of using configure_agent.
- Asking agents to assign platform scopes.
- Reporting internal storage identifiers when slugs/refs are sufficient.
