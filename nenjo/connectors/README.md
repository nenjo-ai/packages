# Connectors

Official Nenjo connector definitions for tool runtimes supplied by the worker
or its environment.

## Agent Browser

The `agent_browser` MCP server exposes browser automation to agents that are
explicitly assigned the connector. Its manifest intentionally does not include
an executable path or arguments. The worker recognizes the
`runtime.connector: agent_browser` metadata, resolves `agent-browser` from its
own `PATH`, and applies the worker's connector security policy.

Installing this package does not grant browser access to every agent. Assign
the installed Agent Browser MCP server only to agents that need it. Workers
without the `agent-browser` CLI installed leave the connector unavailable.
