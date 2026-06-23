# Commands

The `commands` package provides official Nenjo slash commands for explicit
one-turn prompt workflows.

Use this package when you want reusable command entrypoints without installing
the full Nenji agent package.

## What It Includes

| Command | Purpose |
| --- | --- |
| `/design-agent` | Design focused Nenjo agents with role, prompts, memory, model, and capability assignments. |
| `/design-workflow` | Design Nenjo resources, workflows, agents, and cross-resource compositions with knowledge-guided context. |

## How To Use

Install the official packages registry with `nenpm`. In a repo-backed install
from `https://github.com/nenjo-ai/packages.git`, this package is exposed as
`@nenjo-ai/commands`.

Packages that use these commands should declare the dependency:

```yaml
dependencies:
  commands: "^1.1.0"
```

Commands are runtime resources installed from package modules. They are not
referenced with `pkg.*` prompt variables.
