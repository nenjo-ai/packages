# Library — Platform Source Material

## Purpose

The Library is the platform-centered source material surface. It stores explicit
knowledge items and exposes them to agents through knowledge pack metadata,
search, graph traversal, and full-document reads.

Library knowledge is separate from projects, memory, artifacts, and packages.

## What Belongs In The Library

- Product and architecture references.
- Policy, procedure, and compliance material.
- Domain glossaries and classification references.
- Research notes and source-backed guidance.
- Project-relevant references that should remain explicit source material.

## What Does Not Belong In The Library

- Learned preferences or corrections; use memory.
- Generated outputs; use artifacts.
- Task definitions; use project tasks.
- Package manifests; use packages and package manifests.

## Tools

Use these tools to retrieve Library knowledge:

- `list_knowledge_packs`
- `search_knowledge`
- `list_knowledge_neighbors`
- `read_knowledge_doc`

Use these tools to maintain Library knowledge only when the active mode has the
proper Library write surface:

- `create_knowledge_pack`
- `update_knowledge_pack`
- `create_knowledge_doc`
- `update_knowledge_doc`
- `delete_knowledge_doc`

There is no agent-facing delete tool for knowledge packs. Archive a
user-managed pack with `update_knowledge_pack` when a pack should no longer be
active.

## Agent Guidance

When a user asks about source material, documentation, policies, or project
reference knowledge, route to the Library. Read source docs before answering and
cite the selectors or titles used in the reasoning.
