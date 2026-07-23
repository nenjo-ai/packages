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
  capabilities: nenjo/capabilities/package.yaml
  commands: nenjo/commands/package.yaml
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
version: "1.3.0"

dependencies:
  capabilities: "^1.0.0"
  commands: "^1.2.0"
  context: "^1.1.0"
  knowledge: "^1.3.0"

modules:
  - context/
  - agent.yaml
```

Package manifests define package identity, version, dependencies, and root module
entrypoints. Module paths are package-relative.

## Module Resolution

- Modules can point to resource manifest files or directories.
- Directory modules require `index.yml` or `index.yaml`.
- Wrapper-level `imports` compose local package resources transitively.
- Module bundles can contain multiple resources under `nenjo.modules.v1`.
- Every package resource declares an immutable top-level `slug`.
- Cross-package assignments use repository-qualified logical refs such as
  `pkg:@nenjo-ai/packages:capabilities:ability:manage-tasks`.
- Logical refs are stable and versionless; installation records retain the
  exact package version.

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
{{ pkg.nenjo_ai.packages.context.tools.host_tools }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
```

Runtime resources installed from packages are not referenced as `{{ pkg.* }}`
prompt variables.

## Agent Guidance

Explain package structure and selectors. Do not claim an agent can audit, mutate,
install, or publish packages unless the platform adds an explicit package write
surface and the user activates it.
