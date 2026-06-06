# Knowledge Manifests

## Purpose
Use this reference when authoring knowledge pack manifests.

Authored knowledge manifests describe document metadata and graph relationships. They do not carry derived runtime fields.

## Canonical Shape

```yaml
schema: nenjo.knowledge.v1
manifest:
  name: Nenjo Core
  description: Core knowledge for Nenjo agents.
  pack_id: nenjo.core
  docs:
    - selector: design.agents
      source_path: docs/design/agent-design.md
      title: Agent Design
      summary: How to design effective Nenjo agents.
      kind: design
      tags: [resource:agents, intent:design]
      related:
        - type: references
          target: resources.agents
```

## Authored Fields

| Field | Meaning |
|---|---|
| `selector` | Stable agent-visible lookup key |
| `source_path` | Pack-local markdown file path |
| `title` | Human-readable title |
| `summary` | Routing hint used before reading the full doc |
| `kind` | User-defined document category such as `design`, `resource`, `build`, `classification`, or `reference` |
| `tags` | Sparse search facets |
| `related` | Explicit graph edges |

## Agent Guidance

Use this when creating or validating knowledge metadata. The `kind` field is user-defined pack metadata, not a closed platform enum. Use `reference.selector_conventions` for selector syntax and `reference.knowledge_doc_kinds` for kind selection.
