# Search Patterns

## Purpose

Use search patterns to choose how Nenji should retrieve knowledge before
answering, designing, or building.

## Patterns

| User Intent | First Docs To Search | Follow With |
|---|---|---|
| General explanation | `orientation.*` | `reference.glossary` and relevant `resources.*` |
| Design recommendation | `design.resource_design` | matching `design.*`, `resources.*`, and `classification.*` |
| Build or edit resource | `building.*` | matching resource docs, dependency order, and tool guidance |
| Package question | `resources.packages` | `building.package_manifests` and `orientation.sdk` |
| Library/source-material question | `resources.library` | `resources.knowledge_packs` and knowledge tools |
| Permission/tool unavailable | `reference.platform_scopes` | affected resource doc and live tool state |
| Workflow planning | `design.workflows` | `classification.workflow_patterns`, `building.workflow_pattern_cookbook`, `resources.tasks`, `resources.routines` |
| Knowledge authoring | `design.knowledge` | `building.knowledge_manifests`, `reference.selector_conventions` |

## Retrieval Order

1. Search metadata first.
2. Read the most specific design, resource, build, or reference doc.
3. Traverse outbound related docs for dependencies and exact rules.
4. Read selected full docs only when needed.
5. Answer from the docs actually read.

`list_knowledge_neighbors` traverses outbound edges from the selected document.
Seed documents must point outward to important reference docs for traversal to
work reliably.

## Agent Guidance

Prefer selectors and graph edges over reading many full documents. For live
mutable facts, read platform state with the relevant platform tools.
