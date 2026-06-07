# Package Manifests

## Purpose

Use this reference when authoring or explaining `nenjo.package.v1` manifests and
repo-backed package registries.

Packages are installable reference bundles. They are not currently an
agent-auditable or agent-mutable platform surface.

## Registry Rules

`packages.yaml` is a `nenjo.registry.v1` manifest. Repo-backed registries list
unscoped package names:

```yaml
schema: nenjo.registry.v1
packages:
  knowledge: nenjo/knowledge/package.yaml
  nenji: nenjo/nenji/package.yaml
  context: nenjo/context/package.yaml
```

The installed package scope is derived from the package source owner. Do not
author GitHub-style scopes in repo-backed package keys or package `name` fields.

## Package Shape

```yaml
schema: nenjo.package.v1
name: nenji
version: "1.0.0"

dependencies:
  context: "^1.0.0"
  knowledge: "^1.0.0"

modules:
  - context/
  - domains/creator.yaml
  - agent.yaml
```

Package manifests define package identity, version, dependencies, and root module
entrypoints. Module paths are package-relative.

## Module Resolution

- Modules can point to resource manifest files or directories.
- Directory modules require `index.yml` or `index.yaml`.
- Wrapper-level `imports` compose local package resources transitively.
- Module bundles can contain multiple resources under `nenjo.modules.v1`.
- Package assignments resolve package-local paths to runtime slugs/refs.

Supported package resource kinds include agents, abilities, domains, context
blocks, knowledge packs, MCP servers, script tools, commands, hooks, skills, and
plugins.

## Relationship To Knowledge

Knowledge manifests inside packages omit derived runtime fields:

- package `version` comes from the containing package;
- resolved root URI comes from the module location;
- content hash is computed from resolved manifest and content.

Package knowledge is read-only source material for agents after installation.
Library knowledge is the platform-maintained source material surface.

## Prompt Selectors

The `pkg.*` namespace is only for package-installed context blocks and package
knowledge variables:

```jinja
{{ pkg.nenjo_ai.packages.context.tools.tool_usage }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
```

Runtime resources installed from packages are not referenced as `{{ pkg.* }}`
prompt variables.

## Agent Guidance

Explain package structure and selectors. Do not claim an agent can audit, mutate,
install, or publish packages unless the platform adds an explicit package write
surface and the user activates it.
