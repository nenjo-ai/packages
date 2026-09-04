# Platform Scopes

## Purpose

Platform scopes are permission refs that determine which platform tools an
agent or ability can access at runtime.

Scopes are a platform/user control surface. Agents may read and explain scopes,
but they must not assign, escalate, or mutate platform scopes for themselves or
for other resources.

## Common Scope Families

- `agents:read` / `agents:write`
- `abilities:read` / `abilities:write`
- `commands:read` / `commands:write`
- `routines:read` / `routines:write`
- `tasks:read` / `tasks:write`
- `artifacts:read` / `artifacts:write`
- `projects:read` / `projects:write`
- `councils:read` / `councils:write`
- `context_blocks:read` / `context_blocks:write`
- `models:read` / `models:write`
- `knowledge:read` / `knowledge:write`
- `notify:read` / `notify:write`

Available scopes can evolve with the platform. Read current platform state or
tool policy before asserting exact availability.

Artifact agent tools use the dedicated artifact scope family:

- `artifacts:read` exposes `list_artifacts` and `read_artifact`;
- `artifacts:write` additionally exposes `upload_artifact` and implies artifact
  read access.

Task scopes do not grant artifact access, and artifact scopes do not grant task
access. Scope assignment remains a user/platform action.

`knowledge:read` exposes knowledge discovery, graph traversal, and document
reads. `knowledge:write` adds Library pack/document mutation and implies read
access.

The broader organization/API scope catalog also includes `mcp_servers:read`,
`mcp_servers:write`, `media:read`, and `media:write`. The current model-facing
platform tool factory does not expose an MCP server catalog or mutation tool,
and assigned external MCP tools are controlled by resource assignment rather
than those scope strings. Do not add MCP server scopes to an agent merely to
let it use an already assigned server.

Organization-member API authorization separately uses `reviews:read` and
`reviews:write` for the dashboard review inbox and decisions. Those are not
agent manifest tool scopes, and agents should not be configured to impersonate
human reviewers.

## Agent Guidance

- Explain which scope family would be needed for a requested operation.
- Do not include scope changes in agent-authored resource mutations.
- Ask the user or platform administrator to grant scopes when required.
- If a tool is unavailable, report the missing scope family and stop.
