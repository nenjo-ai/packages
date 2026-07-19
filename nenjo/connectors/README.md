# Connectors

Official Nenjo connector definitions for tool runtimes supplied by the worker
or its environment.

## Agent Browser

The `agent_browser` MCP server exposes browser automation to agents that are
explicitly assigned the connector. Its manifest declares the standard
`agent-browser mcp --tools core` command. The worker recognizes the
`nenjo.managed_connector: agent_browser` metadata, resolves the executable from
its own `PATH`, and applies the worker's connector security policy.

Installing this package does not grant browser access to every agent. Assign
the installed Agent Browser MCP server only to agents that need it. Workers
without the `agent-browser` CLI installed leave the connector unavailable.
