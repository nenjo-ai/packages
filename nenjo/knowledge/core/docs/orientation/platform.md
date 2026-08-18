# Platform

## Purpose

The Nenjo Platform is the user-facing surface for working with agents,
projects, Library knowledge and artifacts, routines, human reviews, councils,
models, workers, and settings.

## Main Areas

- Chat for direct interaction with agents and councils.
- Agent builder for behavior, prompts, model selection, and assignments.
- Routine editor for workflow graphs, gates, durable human-review steps,
  retries, and councils.
- Task pages for organization work, optional project association, execution
  targets, schedules, and run history.
- Council editor for leaders, members, strategy, and collaboration structure.
- Project pages for repository context and project settings.
- Library with separate Knowledge and Artifacts tabs: source-material search
  and graph relationships for knowledge, and filesystem-like immutable file
  browsing and rendering for artifacts.
- Reviews for pending and resolved human workflow decisions.
- Settings for models, workers, security posture, and user-controlled permission
  setup.

## Library-Centered Knowledge

Source material belongs in the Library. Library items are exposed to agents as
knowledge packs so they can list packs, search metadata, traverse graph
neighbors, and read selected full content.

Projects can use Library knowledge, but projects should not be treated as
document containers. Keep project metadata, tasks, repository context, and
executions separate from Library source material.

Artifacts may be attached to chat messages and tasks, referenced from routine
handoffs, or browsed in Library. Uploading an artifact does not automatically
index it as knowledge.

## Security And Permissions

Platform scopes control which tool families an agent can access. Scope
assignment is controlled by users and platform policy. Agents may explain missing
scope families, but must not assign or escalate scopes.

Treat worker routing, tenant isolation, prompt security, and encrypted resource
content as security-sensitive platform behavior. Query live state before making
mutable claims.

## Tool Guidance

Use resource-specific read tools before writes. For Library retrieval use
`list_knowledge_packs`, `search_knowledge`, `list_knowledge_neighbors`, and
`read_knowledge_doc`. For Library writes use `create_knowledge_doc`,
`update_knowledge_doc`, and `delete_knowledge_doc` only in an active mode with
the proper write surface.

For artifacts use `list_artifacts`, `read_artifact`, and—when explicitly
authorized—`upload_artifact`. Use the Reviews page for human decisions; agents
author review topology in routines but do not impersonate the reviewer.
