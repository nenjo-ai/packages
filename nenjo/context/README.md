# Context

The `context` package provides reusable prompt context blocks for common agent
operating rules. It is intentionally generic: the blocks can be shared by
agents, domains, and abilities that need consistent guidance for tool use,
memory, delegation, knowledge retrieval, write safety, and debugging.

Use this package when prompts need durable operating guidance that should be
composed by selector instead of copied into every agent or ability.

## What It Includes

| Area | Selector | Purpose |
| --- | --- | --- |
| Agents | `pkg.nenjo_ai.packages.context.agents.ability_orchestration` | Select, sequence, verify, and synthesize ability calls. |
| Agents | `pkg.nenjo_ai.packages.context.agents.sub_agents` | Decide when and how to delegate to sub-agents. |
| Knowledge | `pkg.nenjo_ai.packages.context.knowledge.knowledge_routing` | Use metadata-first retrieval and graph traversal. |
| Memory | `pkg.nenjo_ai.packages.context.memory.remembrance` | Recall, store, and update durable memory and artifact context. |
| Operations | `pkg.nenjo_ai.packages.context.operations.failure_modes` | Classify failures and debug from evidence. |
| Operations | `pkg.nenjo_ai.packages.context.operations.write_discipline` | Sequence, risk-classify, and verify writes. |
| Runtime | `pkg.nenjo_ai.packages.context.runtime.chat_response` | Generic chat runtime input and response rules. |
| Runtime | `pkg.nenjo_ai.packages.context.runtime.task_execution` | Generic routine task-step input, `pass_verdict`, project context, metadata, and reporting rules. |
| Runtime | `pkg.nenjo_ai.packages.context.runtime.gate_evaluation` | Generic gate runtime input, `pass_verdict`, and verdict rules. |
| Runtime | `pkg.nenjo_ai.packages.context.runtime.heartbeat_check` | Generic heartbeat runtime input and reporting rules. |
| Tools | `pkg.nenjo_ai.packages.context.tools.tool_usage` | Choose, sequence, and parallelize tools safely. |

## How To Use

Install the official packages registry with `nenpm`. In a repo-backed install
from `https://github.com/nenjo-ai/packages.git`, this package is exposed as
`@nenjo-ai/context`.

Packages that use these blocks should declare the dependency:

```yaml
dependencies:
  context: "^1.0.0"
```

Then reference the blocks in prompts by package selector:

```jinja
{{ pkg.nenjo_ai.packages.context.tools.tool_usage }}
{{ pkg.nenjo_ai.packages.context.operations.write_discipline }}
{{ pkg.nenjo_ai.packages.context.knowledge.knowledge_routing }}
```

Use the smallest context block that matches the behavior you want. For example,
an ability that performs writes usually needs `write_discipline` and
`tool_usage`; an agent that retrieves docs usually needs `knowledge_routing`;
an orchestration-heavy agent may need `ability_orchestration` or `sub_agents`.

## Authoring Notes

Context blocks are prompt variables. Installed agents, abilities, domains,
routines, MCP servers, and other resource manifests are resolved through
package modules and runtime imports instead of `pkg.*` prompt references.

Inside a package, local manifests can import local context files by relative
module path. Across installed packages, use the package selector form shown
above.
