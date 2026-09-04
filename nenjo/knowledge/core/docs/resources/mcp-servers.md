# MCP Servers — External Tool Integrations

## Purpose

MCP servers are external tool integrations assigned to agents or abilities by
slug/ref. They provide hosted or local tool surfaces outside the core platform
tools.

## Authoring And Assignment

MCP server manifests can come from packages or controlled platform setup.
Agents and abilities reference MCP servers through `mcp_servers` assignment
lists.

The current model-facing platform surface does not expose an MCP server catalog
or mutation tool. Builder abilities may wire an exact existing MCP server ref
when updating an agent or ability, but they must not invent or claim to verify
that ref through a nonexistent catalog tool.

## Agent Guidance

- Reuse exact MCP server refs supplied by the user or already present on a
  resource; allow the configure call to reject a missing ref.
- Keep MCP assignments minimal and purpose-specific.
- Treat external tool access as sensitive.
- Report missing MCP refs instead of inventing them.
