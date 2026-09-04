# Ability Design

## Purpose
Use ability design when deciding whether a capability should be exposed to an agent as a callable specialist tool.

Abilities are best for narrow, reusable work with a clear activation condition
and result shape. They run as nested executions, inherit the caller's system
invariants and memory configuration, and replace its developer guidance with
their own specialist prompt.

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
- which agents should receive the ability;
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
- broad recurring behavior that belongs to the parent agent.

## Boundary Rules

- If the parent agent should decide when to invoke it, use an ability.
- Keep permission changes on a user-controlled platform surface.
- If the work has multiple persistent steps or gates, use a routine.

## Agent Guidance

Use this doc to design capability boundaries. Read `resources.abilities` for
the runtime model and `design.commands` when a user-invoked one-turn entrypoint
is required.
