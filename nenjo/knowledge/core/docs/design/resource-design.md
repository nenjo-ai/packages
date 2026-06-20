# Resource Design

## Purpose

Use resource design when deciding what Nenjo resource should represent a user
request and what should be handed to a build ability.

The goal is a minimum viable resource spec: enough structure to build safely,
without inventing unrelated topology or permissions.

## Resource Boundary Heuristics

| User Need | Resource |
|---|---|
| Ongoing behavioral owner | agent |
| Narrow callable specialist behavior | ability |
| User-invoked one-turn prompt workflow | command |
| Explicit user-activated mode | domain |
| Reusable prompt guidance | context block |
| Multi-step workflow, gates, retries, or schedules | routine |
| Structured multi-agent collaboration | council |
| Trackable project work | task |
| Workspace boundary for tasks, executions, settings, and repo context | project |
| Source material, policy, docs, or graph-retrievable knowledge | library knowledge |
| Installable bundle of resources | package |
| External integration surface | MCP server |
| Native or package script-backed tool | script tool |
| Permission needed for platform tools | platform scope |

Packages and platform scopes require special handling. Packages are reference
and install bundles, not currently agent-auditable or agent-mutable resources.
Platform scopes are user/platform-controlled permission refs; agents may explain
needed scopes but must not assign them.

## Workflow Resource Tradeoffs

| Need | Prefer | Why |
|---|---|---|
| Repeatable business process with step audit | routine | The graph records steps, edges, gates, joins, and retry outcomes |
| Fuzzy intent routing or adaptive execution | agent | The model can choose tools and paths from context |
| Audited parallel branches and joins | routine | `entry_steps`, fan-out edges, and all-success joins are explicit |
| Multi-perspective judgment | council | Distinct members provide critique, votes, or synthesis |
| Narrow reusable operation called by a parent | ability | Smaller than a new agent or routine |
| Explicit user slash entrypoint for one chat turn | command | The user chooses the prompt workflow without changing modes |
| User-approved mode switch | domain | Mode and capability changes are explicit to the user |

Do not force every workflow into a routine. Use routines when the topology is a
contract. Use agents or councils when the topology should emerge from reasoning.

## Minimum Viable Spec

Before building, identify:

- resource type;
- stable name and slug/ref;
- purpose and non-goals;
- upstream dependencies;
- required tool surface;
- prompt/context inputs if relevant;
- verification readback;
- whether the operation requires user-controlled scopes or package installation.

## Routing Guidance

- Use `resources.agents`, `resources.abilities`, and `resources.domains` for
  behavior and mode design.
- Use `resources.commands` when the user wants an explicit slash command that
  reshapes a chat turn.
- Use `resources.routines`, `resources.councils`, and `resources.tasks` for
  workflow design.
- Use `classification.workflow_patterns` and
  `building.workflow_pattern_cookbook` when a workflow pattern has several
  valid implementations.
- Use `resources.library` and `resources.knowledge_packs` for source material.
- Use `resources.packages` and `building.package_manifests` for package
  structure questions.
- Use `reference.platform_scopes` when the question is about permissions.

## Pitfalls To Avoid

- Modeling Library source material as project metadata.
- Modeling user-activated privilege changes as abilities.
- Modeling a one-turn slash prompt as a domain or routine.
- Asking agents to assign platform scopes.
- Treating packages as agent-writable resources.
- Adding workflow topology when a task or ability is enough.
