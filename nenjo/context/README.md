# Context

The `context` package provides reusable prompt context blocks for common agent
operating rules. It is intentionally generic: the blocks can be shared by
agents and abilities that need consistent guidance for tool use,
memory, delegation, knowledge retrieval, write safety, and debugging.

Use this package when prompts need durable operating guidance that should be
composed by selector instead of copied into every agent or ability.

## What It Includes

| Area | Selector | Purpose |
| --- | --- | --- |
| Agents | `pkg.nenjo_ai.packages.context.agents.agent_delegation` | Delegate bounded work to installed agents with their own durable capability surface. |
| Agents | `pkg.nenjo_ai.packages.context.agents.ability_orchestration` | Select, sequence, verify, and synthesize ability calls. |
| Agents | `pkg.nenjo_ai.packages.context.agents.sub_agents` | Decide when and how to delegate to sub-agents. |
| Knowledge | `pkg.nenjo_ai.packages.context.knowledge.knowledge_routing` | Use metadata-first retrieval and graph traversal. |
| Memory | `pkg.nenjo_ai.packages.context.memory.remembrance` | Recall and update durable memory while keeping immutable artifacts on their tool-backed surface. |
| Operations | `pkg.nenjo_ai.packages.context.operations.failure_modes` | Classify failures and debug from evidence. |
| Operations | `pkg.nenjo_ai.packages.context.operations.write_discipline` | Sequence, risk-classify, and verify writes. |
| Tools | `pkg.nenjo_ai.packages.context.tools.artifact_tools` | Browse, inspect, publish, and revise immutable organization artifacts. |
| Tools | `pkg.nenjo_ai.packages.context.tools.host_tools` | Choose between scoped filesystem, repository, and shell tools. |

## How To Use

Install the official packages registry with `nenpm`. In a repo-backed install
from `https://github.com/nenjo-ai/packages.git`, this package is exposed as
`@nenjo-ai/context`.

Packages that use these blocks should declare the dependency:

```yaml
dependencies:
  context: "^1.5.0"
```

Then reference the blocks in prompts by package selector:

```jinja
{{ pkg.nenjo_ai.packages.context.tools.host_tools }}
{{ pkg.nenjo_ai.packages.context.tools.artifact_tools }}
{{ pkg.nenjo_ai.packages.context.operations.write_discipline }}
{{ pkg.nenjo_ai.packages.context.knowledge.knowledge_routing }}
```

Use the smallest context block that matches the behavior you want. For example,
an ability that performs writes may need `write_discipline`; add `host_tools`
only when it operates on workspace files or commands. An agent that retrieves
docs may need `knowledge_routing`; an orchestration-heavy agent may need one or
more delegation blocks.

## Authoring Notes

Context blocks compile into the static instruction prefix. They may use declared
package arguments and other static context or knowledge selectors, but not agent
identity or live chat, task, project, routine, gate, Git, clock, memory, or
artifact state. Agent identity comes from runtime session control. Installed
agents, abilities, routines, MCP servers, and other resource manifests
are resolved through package modules and runtime imports instead of `pkg.*`
prompt references.

Inside a package, local manifests can import local context files by relative
module path. Across installed packages, use the package selector form shown
above.
