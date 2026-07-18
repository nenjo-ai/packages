# Platform Scopes

## Purpose

Platform scopes are permission refs that determine which platform tools an
agent, ability, or domain can access at runtime.

Scopes are a platform/user control surface. Agents may read and explain scopes,
but they must not assign, escalate, or mutate platform scopes for themselves or
for other resources.

## Common Scope Families

- `agents:read` / `agents:write`
- `abilities:read` / `abilities:write`
- `domains:read` / `domains:write`
- `commands:read` / `commands:write`
- `routines:read` / `routines:write`
- `tasks:read` / `tasks:write`
- `projects:read` / `projects:write`
- `councils:read` / `councils:write`
- `context_blocks:read` / `context_blocks:write`
- `models:read` / `models:write`
- `mcp_servers:read` / `mcp_servers:write`
- `library:write`

Available scopes can evolve with the platform. Read current platform state or
tool policy before asserting exact availability.

## Agent Guidance

- Explain which scope family would be needed for a requested operation.
- Do not include scope changes in agent-authored resource mutations.
- Ask the user or platform administrator to grant scopes when required.
- If a tool is unavailable, report the missing scope family and stop.
