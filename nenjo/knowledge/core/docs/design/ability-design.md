# Ability Design

## Purpose
Use ability design when deciding whether a capability should be exposed to an agent as a callable specialist tool.

Abilities are best for narrow, reusable work with a clear activation condition and result shape. They are essentially sub-agents that inherit the parent agents identity and memory.

## Design Questions

- Is this capability narrow enough to be called like a tool?
- Can the parent agent decide when to invoke it?
- Does the capability need its own developer guidance?
- Should the output be returned to the parent agent instead of owning long-running state?

## Good Ability Shape

A good ability has:

- a crisp activation condition;
- a bounded input and output expectation;
- specialist developer guidance;

## Minimum Ability Design

Include:

- activation condition in one or two sentences;
- parent-agent input expectations;
- result shape expected by the parent agent;
- developer guidance for the specialist behavior;
- required platform scopes or MCP servers;
- whether the ability is assigned directly to an agent or exposed only through a domain;
- verification plan for output quality.

## Use An Ability For

- code review;
- extraction;
- validation;
- summarization;
- risk checks;
- format conversion.

## Avoid An Ability For

- long-running ownership;
- user-approved mode switches;
- multi-step workflow graphs;
- behavior that should define the agent identity.

## Boundary Rules

- If the parent agent should decide when to invoke it, use an ability.
- If the user must explicitly approve the mode before use, use a domain.
- If the work has multiple persistent steps or gates, use a routine.

## Agent Guidance

Use this doc to design capability boundaries. Read `resources.abilities` for the runtime model and `design.domains` when explicit user activation is required.
