# Nenji

Nenji is the official Nenjo platform guide package. It installs the `Nenji`
agent, a user-activated `#creator` domain, build abilities, and Nenji-specific
context blocks for understanding, designing, reviewing, and safely changing
Nenjo resources.

Use this package when you want an agent that can explain how Nenjo works,
inspect live platform state, design resource changes, and route platform writes
through explicit write-enabled mode.

## What It Includes

- `agent.yaml`: the `Nenji` agent manifest.
- `domains/creator.yaml`: the `#creator` domain for write-enabled platform
  changes.
- `capabilities/build/`: write abilities for agents, abilities, domains,
  context blocks, Library knowledge, councils, routines, and project work.
- `context/`: Nenji-specific context blocks for methodology and resource
  surface routing.

The package depends on:

```yaml
dependencies:
  context: "^1.0.0"
  knowledge: "^1.0.0"
```

Those dependencies provide shared operating context and the Nenjo Core knowledge
pack used by Nenji's prompts and abilities.

## How To Use

Install the official packages registry with `nenpm`. In a repo-backed install
from `https://github.com/nenjo-ai/packages.git`, this package is exposed as
`@nenjo-ai/nenji`.

After installation, select the `Nenji` agent in the platform for read, explain,
design, review, or debug work. Nenji can inspect state and propose changes in
normal mode.

Use `#creator` only when you want Nenji to create or modify platform resources:

```text
#creator create a focused release manager agent for this project
```

Creator mode enables the write abilities installed by this package:

- `build_agent`
- `build_ability`
- `build_domain`
- `build_context`
- `build_library`
- `build_council`
- `build_routine`
- `manage_project_work`

Nenji's write flow is intentionally conservative: it reads current state before
mutation, uses the narrowest matching ability, and verifies writes by reading
back the changed resource.

## Prompt And Package References

Installed agents, domains, and abilities are resolved through package modules
and runtime imports. They are not referenced with `pkg.*` prompt variables.

Nenji's prompts do reference context blocks and knowledge from its dependencies,
for example:

```jinja
{{ pkg.nenjo_ai.packages.context.agents.ability_orchestration }}
{{ pkg.nenjo_ai.packages.context.operations.write_discipline }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
```

Use those selectors when composing prompts that need the same shared guidance.
