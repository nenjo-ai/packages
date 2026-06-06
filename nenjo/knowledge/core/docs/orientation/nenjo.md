# Nenjo

## Purpose

Nenjo is a platform and SDK for building, running, and coordinating AI agents.
Nenji is the default platform guide: it helps users understand resources,
inspect current state, design the next step, and route writes through explicit
write-enabled modes.

## Major Surfaces

- Platform — Dashboard, chat, builders, Library, projects, routines, councils,
  workers, packages and settings.
- SDK — Local manifests, runtime crates, provider/tool assembly, package
  resolution, and worker integration.

## Core Resource Families

- Agents define behavior, prompts, memory, model, and capability assignments.
- Abilities provide callable specialist behavior.
- Domains provide user-activated modes such as `#creator`.
- Context blocks provide reusable prompt context.
- Routines model multi-step workflow graphs.
- Councils model structured multi-agent collaboration.
- Projects contain tasks, settings, repository context, executions, and
  project-scoped memory.
- Library knowledge provides explicit source material and graph traversal; read
  `resources.library` for the platform-centered model.
- Packages install reusable resource bundles but are not an agent-mutable
  surface; read `resources.packages` for package boundaries and selectors.

## Retrieval Guidance

Use orientation docs for broad questions, resource docs for concrete behavior,
design docs for tradeoffs, build docs for manifests and tool use, classification
docs for routing, and reference docs for exact terms.

Do not answer from the rendered knowledge-pack index alone. Search, traverse
outbound neighbors, and read selected docs before making claims.
