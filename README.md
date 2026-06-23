# Nenjo Packages

Official Nenjo package registry for repo-backed knowledge and manifest
resources.

## Registry

`packages.yaml` is a `nenjo.registry.v1` manifest. Repo-backed registries list
unscoped package names. When installed from GitHub, nenpm exposes them under
the package scope derived from the GitHub org:

```yaml
schema: nenjo.registry.v1
packages:
  commands: nenjo/commands/package.yaml
  knowledge: nenjo/knowledge/package.yaml
  nenji: nenjo/nenji/package.yaml
  context: nenjo/context/package.yaml
```

For this repository, `https://github.com/nenjo-ai/packages.git` installs those
entries as `@nenjo-ai/commands`, `@nenjo-ai/knowledge`, `@nenjo-ai/nenji`, and
`@nenjo-ai/context`.
The scope is not authored in `packages.yaml`; it comes from the GitHub owner.
Local test registries can set a scope in `nenpm.yml`, but published repo-backed
registries must keep package keys and package manifest `name` fields unscoped.

## Packages

Each package is a versioned dependency unit with package-level dependencies and
a list of root module entrypoints:

```yaml
schema: nenjo.package.v1
name: nenji
version: "1.0.0"

dependencies:
  knowledge: "^1.0.1"

modules:
  - nenji/agent.yaml
```

Modules are package-relative resource manifest paths or directory references
that start resolution. The resolver follows wrapper-level local imports
transitively, so `nenji` only needs to list `nenji/agent.yaml`; the agent
imports its capabilities, domain, and context modules. Directory references
must contain an explicit `index.yml` or `index.yaml` file; the resolver never
imports a directory by implicit file listing. The installer infers runtime
behavior from each resolved module manifest schema, for example
`nenjo.agent.v1`, `nenjo.ability.v1`, `nenjo.context_block.v1`, or
`nenjo.knowledge.v1`.

```yaml
# nenji/capabilities/build/index.yml
schema: nenjo.module_index.v1
modules:
  - agent.yaml
  - build_routine.yaml
```

Module files may also bundle multiple resources:

```yaml
schema: nenjo.modules.v1
resources:
  - schema: nenjo.ability.v1
    manifest:
      name: build_agent
  - schema: nenjo.ability.v1
    manifest:
      name: build_routine
```

Bundled resources are referenced with `#resource_name` selectors. The official
packages currently use single-resource module files, so imports can point
directly at files or indexed directories without fragments. Nenji's installed
write capability modules are grouped under `nenjo/nenji/capabilities/build/`;
package dependencies and runtime imports live in `*.package.yaml` and the module
manifests instead of per-ability directories.

## Prompt Selectors

The `pkg.*` namespace is only for prompt/template references to package-installed
context blocks and package knowledge.

Examples:

```jinja
{{ context.methodology }}
{{ pkg.nenjo_ai.packages.context.memory.remembrance }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
```

Agents, abilities, domains, routines, MCP servers, and other installed resource
manifests are resolved through package modules/imports and the worker/dashboard
runtime. They are not referenced as `{{ pkg.* }}` prompt variables.

## Imports

Resource manifests declare local runtime imports at the module wrapper level,
outside the pure runtime `manifest` body:

```yaml
schema: nenjo.agent.v1
imports:
  abilities:
    - ./capabilities/build/
  context:
    - ./shared/context/methodology.yml

manifest:
  name: Nenji
```

Imports describe SDK runtime composition and must be local file/module refs.
Package dependencies still declare versioned package requirements. Context
package variables are derived from package/module paths. Knowledge package
variables include source scope, source repo, package name, and knowledge pack
name, such as `{{ pkg.nenjo_ai.packages.knowledge.core }}`. Rendered knowledge
metadata and `list_knowledge_packs` expose the canonical tool selector, such as
`pkg:nenjo-ai.packages.knowledge.core`.

Package manifests do not author dashboard organization paths. For abilities,
domains, and context blocks, `manifest.path` is derived from the module's
package-relative directory during package resolution/import.

## Current Package Set

This repository exposes four top-level packages:

- `@nenjo-ai/commands`
- `@nenjo-ai/knowledge`
- `@nenjo-ai/nenji`
- `@nenjo-ai/context`
