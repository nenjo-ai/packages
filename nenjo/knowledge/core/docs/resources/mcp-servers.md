# MCP Servers — External Tool Integrations

## Purpose

MCP servers are external tool integrations assigned to agents, abilities, or
domains by slug/ref. They provide hosted or local tool surfaces outside the core
platform tools.

## Authoring And Assignment

MCP server manifests can come from packages or controlled platform setup.
Agents, abilities, and domains reference MCP servers through `mcp_servers`
assignment lists.

Do not imply a generic agent-write MCP server mutation tool unless the active
platform exposes one. Builder abilities may wire existing MCP server refs when
the user explicitly requests it and the refs have been read or otherwise
verified.

## Agent Guidance

- Read or verify available MCP server refs before assigning them.
- Keep MCP assignments minimal and purpose-specific.
- Treat external tool access as sensitive.
- Report missing MCP refs instead of inventing them.
