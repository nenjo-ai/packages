# Selector Conventions

## Purpose
Use this reference for knowledge document selector naming.

Selectors are stable agent-visible keys. They are not file paths.

## Syntax

Selectors use dot-separated Jinja-compatible identifier segments:

```text
design.agents
resources.context_blocks
building.knowledge_manifests
```

Each segment should:

- start with a letter or underscore;
- contain only letters, numbers, and underscores;
- avoid hyphens, spaces, and path separators.

## Naming Rules

- Use plural resource names when the doc describes a resource class.
- Use singular concept names when the doc describes one convention.
- Keep selectors stable when files move.
- Prefer semantic groups: `orientation`, `design`, `resources`, `building`, `classification`, `reference`.

## Agent Guidance

Use selectors for tool calls and related edges. Use `source_path` only as storage metadata.
