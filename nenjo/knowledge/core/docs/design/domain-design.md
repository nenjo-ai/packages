# Domain Design

## Purpose
Use domain design when a user needs an explicit mode change that alters behavior, available abilities, or available tools for a session.

Domains are user-activated operating modes. They are not ordinary agent-invoked tools.

## Design Questions

- Does this mode require explicit user intent?
- What behavior changes while the domain is active?
- Which abilities or tools become available in this mode?
- How should activation and deactivation be visible to the user?

## Good Domain Shape

A good domain has:

- a memorable command;
- clear user-facing purpose;
- explicit behavior delta;
- limited added capability surface;
- auditable session semantics.

## Minimum Domain Design

Include:

- activation command;
- display name and user-facing description;
- behavior added or changed while active;
- abilities, tools, scopes, or MCP servers exposed only in the domain;
- prompt addon that explains the mode boundary;
- exit behavior or user-facing deactivation language;
- verification plan for activation, prompt rendering, and capability visibility.

## Use A Domain For

- sensitive operational modes;
- compliance or legal review;
- production-oriented work;
- strategy or executive-summary modes;
- any behavior that should not silently activate.

## Pitfalls

- Using domains for narrow reusable tools.
- Creating overlapping modes with unclear differences.
- Hiding what changes after activation.
- Assigning write capabilities to the base agent when they should require
  explicit user activation.

## Boundary Rules

- Use a domain for permission expansion, sensitive work, or explicit operating modes.
- Use an ability when the parent can invoke the work without a user mode switch.
- Use a context block when only reusable instructions are needed.
- Keep domains few and memorable; overlapping domains reduce user trust.

## Agent Guidance

Use this when designing user-approved mode switches. Read `resources.domains` for the resource model and `design.abilities` when the capability can be agent-invoked.
