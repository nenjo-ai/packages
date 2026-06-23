# Domains — User-Activated Execution Modes

## Purpose

Domains are user-activated execution modes. A domain changes an agent's behavior
for an explicit session and may expose additional abilities or tool surfaces.
Domains are appropriate when a capability needs user intent, auditability, or a
temporary shift in operating rules.

## What Makes A Domain Different From An Ability

| Aspect | Ability | Domain |
|---|---|---|
| Activation | Invoked by the agent as a tool | Activated explicitly by the user |
| Scope | Narrow specialist behavior | Broader session mode |
| Tool Surface | Focused specialist execution | Mode-specific abilities and tools |
| Use Case | Reusable specialist work | Elevated, sensitive, or strategic modes |

If a capability should only run after explicit user activation, model it as a
domain instead of an ability.

## Core Fields

- `name` — Stable internal name.
- `display_name` — Human-readable name.
- `description` — What changes when the domain is active.
- `command` — User activation command, usually `#name` such as `#review`.
- `abilities` — Ability slugs/refs available in this mode.
- `mcp_servers` — MCP server slugs/refs available in this mode.
- `script_tools` — Script tool slugs/refs available in this mode.
- `platform_scopes` — Platform permission refs. Agents must not grant or change
  these; users or platform-controlled setup must assign them.
- `prompt_config.developer_prompt_addon` — Extra guidance appended while the
  domain session is active.

## Runtime Behavior

When a user activates a domain:

1. The agent receives the domain developer prompt addon.
2. The agent gains the configured mode-specific abilities and tool refs.
3. A domain session is tracked for auditability.
4. The expanded behavior stays active until the user deactivates it or the
   session ends.

## Tools

Use these platform tools for domain work when the caller has the right write
surface:

- `list_domains`
- `get_domain`
- `configure_domain`

Use `list_domains` to discover existing slugs and `get_domain` to inspect the
full DomainDocument, including prompt_config and assignments. Use
`configure_domain` for all domain writes: creation, metadata/command changes,
prompt_config changes, and full replacement ability/MCP/script tool assignments.
Do not use `configure_domain` to assign platform scopes. Platform scopes are
user-controlled permission refs.

## Common Patterns

- `#review` — Review-oriented mode with stricter critique behavior.
- Production mode — Deployment-oriented behavior with stricter audit rules.
- Legal review mode — Specialized review behavior and document-sensitive tools.
- Debug mode — Additional diagnostic behavior for a session.

## Pitfalls To Avoid

- Using domains for narrow behavior an agent could safely invoke as an ability.
- Making high-risk modes too easy to activate.
- Mutating platform scopes from agent-authored resource changes.
- Using slash commands when the product convention is hash commands.
