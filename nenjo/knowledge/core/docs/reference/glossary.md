# Glossary

## Purpose

Use this for compact definitions of common Nenjo terms. For design or build
decisions, traverse to the relevant resource, design, build, or reference doc.

| Term | Meaning |
|---|---|
| Agent | Primary behavioral unit that owns prompts, memory profile, model, and assigned capability refs |
| Ability | Agent-invoked specialist execution mode exposed through a `tool_name` |
| Domain | User-activated execution mode such as `#review` |
| Context Block | Reusable prompt context referenced by a stable path |
| Routine | Workflow graph with steps, edges, gates, retries, and terminal states |
| Council | Structured multi-agent collaboration group |
| Project | Work container for tasks, executions, settings, repository context, and project-scoped memory |
| Task | Organization-owned work item with an optional project, one agent-or-routine execution target, and an optional schedule |
| Execution | One manual, retry, or scheduled invocation of a task |
| Library | Platform source-material surface exposed to agents through knowledge tools |
| Knowledge Pack | Searchable source-material pack with metadata, graph edges, and full-document reads |
| Package | Versioned installable bundle of Nenjo resources; not currently agent-mutable |
| Package Manifest | `nenjo.package.v1` file defining package identity, dependencies, and module entrypoints |
| Package Registry | `nenjo.registry.v1` file mapping package names to package manifests |
| Selector | Stable agent-visible key used to find knowledge or package-installed prompt refs |
| Source Path | Pack-local file path for a knowledge document body |
| Platform Scope | User/platform-controlled permission ref that determines tool availability |
| MCP Server | External tool integration surface assigned by ref |
| Script Tool | Native or package script-backed tool assigned by ref |
| Artifact | Saved generated output or workspace file indexed separately from memory |
| Memory | Learned facts, preferences, corrections, and durable operating context |

## Retrieval Notes

- Use `resources.library` for platform source material.
- Use `resources.packages` for installable bundles and `pkg.*` prompt selectors.
- Use `reference.platform_scopes` for permission/tool availability questions.
- Use `building.resource_dependency_order` before writing platform resources.
