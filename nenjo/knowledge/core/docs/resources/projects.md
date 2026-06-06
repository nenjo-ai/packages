# Projects — Work Containers

## Purpose

Projects are top-level containers for work. They group tasks, settings,
repository connections, execution runs, and project-scoped memory into one
workspace boundary.

Project knowledge does not live as documents inside the project resource.
Structured source material belongs in the Library and is exposed to agents
through knowledge packs and knowledge tools.

## Core Concepts

- A project is the main boundary for tasks and executions.
- Project settings shape execution behavior, repository sync, and workspace
  context.
- Project memory is scoped separately from agent core and shared memory.
- Repository-connected projects provide worktrees for code-backed execution.
- Library knowledge is the source-of-truth documentation surface for project
  reference material.

## Key Fields

- `name` — Human-readable project name.
- `slug` — Stable project ref used in URLs and tool calls.
- `description` — Project purpose and goals.
- `settings` — Project-level configuration.
- repository connection — Optional Git source managed through project settings
  and repository sync actions.

Use slugs and refs in agent-facing explanations. Do not require users to reason
about internal storage identifiers.

## Repository Connections & Worktrees

Projects can be connected to a Git repository. When a project has a synced
repository, task execution can use an isolated worktree so the worker can inspect,
modify, test, and report against code.

Agent implications:

- Treat the synced repository as the workspace for implementation tasks.
- Inspect worktree state before making claims about code changes.
- Tie task acceptance criteria to verifiable repository changes.
- Keep Git/worktree guidance in prompts for code-working agents.

## Library Relationship

The Library owns structured knowledge items and their graph metadata. Project
work can reference library knowledge, but the project resource itself should not
be described as containing documents.

When users ask about project reference material, route them to Library knowledge:

- `list_knowledge_packs`
- `search_knowledge`
- `list_knowledge_neighbors`
- `read_knowledge_doc`

When users need to create or maintain library knowledge and the active mode has
the correct write surface, use:

- `create_knowledge_doc`
- `update_knowledge_doc`
- `delete_knowledge_doc`

## Project Tools

Use project tools for project metadata and lifecycle work:

- `list_projects`
- `get_project`
- `create_project`
- `update_project`
- `delete_project`

Use project-task and project-execution tools for tasks and runs; do not overload
project metadata tools for task or library work.

## Pitfalls To Avoid

- Treating projects as document stores.
- Treating library knowledge as memory.
- Starting execution before task executor refs and dependencies are valid.
- Describing repository-backed changes without inspecting the worktree.
