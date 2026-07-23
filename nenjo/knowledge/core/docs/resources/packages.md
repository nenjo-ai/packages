# Packages — Installable Reference Bundles

## Purpose

Packages are versioned bundles of Nenjo resources. They can provide agents,
abilities, domains, context blocks, knowledge packs, MCP servers, script tools,
commands, hooks, skills, and other installable resources.

In Nenjo Core knowledge, packages are a reference point and package-system
resource. They are not currently an auditable or mutable surface for agents.
Agents may explain packages and use package-installed context or knowledge, but
they should not mutate package manifests, package registries, package installs,
or package-owned source material.

## Core Concepts

- `packages.yaml` is a registry manifest that points to package manifests.
- `nenjo.package.v1` defines package identity, version, dependencies, and module
  entrypoints.
- Package names in repo-backed registries are authored unscoped; the installed
  scope is derived from the source owner.
- Modules are package-relative files or directories with explicit indexes.
- Wrapper-level `imports` compose local resources inside a package.
- Package resources declare stable top-level slugs independent of display names.
- Dependency assignments use versionless, repository-qualified logical refs,
  such as `pkg:@nenjo-ai/packages:capabilities:ability:manage-tasks`.

## Prompt Selectors

The `pkg.*` namespace is only for package-installed context blocks and package
knowledge variables.

Examples:

```jinja
{{ pkg.nenjo_ai.packages.context.tools.host_tools }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
```

Agents, abilities, domains, routines, MCP servers, and script tools are installed
as runtime resources. They are not referenced as `{{ pkg.* }}` prompt variables.

## Agent Guidance

Use this doc when explaining package structure, package-installed prompt
selectors, or how packages relate to runtime resources. Do not present package
installation, package audit, package mutation, or registry editing as an
agent-writable operation.

For manifest authoring details, read `building.package_manifests`.
