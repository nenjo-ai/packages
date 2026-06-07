# Knowledge

The `knowledge` package provides the canonical Nenjo Core knowledge pack. It is
the source of reusable documentation for Nenjo concepts, resource design,
package manifests, prompt structure, context engineering, workflow patterns,
and platform/SDK orientation.

Use this package when an agent or workflow needs grounded answers about how
Nenjo resources are modeled, designed, built, routed, or retrieved.

## What It Includes

- `core/manifest.yaml`: the `nenjo.knowledge.v1` manifest for the Nenjo Core
  knowledge pack.
- `core/docs/orientation/`: high-level guides for Nenjo, the platform, and the
  SDK.
- `core/docs/design/`: design guidance for agents, abilities, domains,
  councils, prompts, workflows, context, and knowledge.
- `core/docs/resources/`: resource-level references for agents, abilities,
  domains, councils, tasks, routines, projects, memory, executions, context
  blocks, and knowledge packs.
- `core/docs/building/`: implementation guidance for manifests, prompt
  structure, template variables, and dependency order.
- `core/docs/classification/` and `core/docs/reference/`: routing
  classifications, selector conventions, user-defined document kinds, and
  glossary material.

## How To Use

Install the official packages registry with `nenpm`. In a repo-backed install
from `https://github.com/nenjo-ai/packages.git`, this package is exposed as
`@nenjo-ai/knowledge`.

Packages that use the core knowledge pack should declare it as a dependency:

```yaml
dependencies:
  knowledge: "^1.0.0"
```

Then seed the pack or specific documents in prompts with package selectors:

```jinja
{{ pkg.nenjo_ai.packages.knowledge.core }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
{{ pkg.nenjo_ai.packages.knowledge.core.building.prompt_structuring }}
```

The full pack selector for knowledge tools is:

```text
pkg:nenjo-ai.packages.knowledge.core
```

## Retrieval Pattern

Treat rendered knowledge variables as discovery metadata, not as the full source
of truth. For source-grounded answers:

1. Use `list_knowledge_packs` if the pack selector is not already available.
2. Use `search_knowledge` to find relevant document metadata.
3. Use `list_knowledge_neighbors` when graph relationships may matter.
4. Use `read_knowledge_doc` for the selected source documents.
5. Answer from the documents actually read, live platform state, and named
   assumptions.

Use this package for explicit documentation and graph retrieval. Use memory for
learned preferences or project facts, and use live tools for mutable platform
state.
