# Template Vars

## Purpose

Template variables are runtime context references available inside prompts and
context block templates. They connect stable prompt text with the active agent,
chat message, task, project, routine, gate, Git context, memory,
artifacts, context blocks, and knowledge-pack indexes.

Use this reference for variable lookup. For composition patterns, read
`building.prompt_structuring`.

## Agent

- `{{ self }}`
- `{{ agent.slug }}`
- `{{ agent.name }}`
- `{{ agent.model }}`
- `{{ agent.description }}`

Use `{{ self }}` when the prompt needs a compact XML-like description of the
executing agent. Use scalar fields for concise identity references.

## Chat

- `{{ chat.message }}`

Use this in chat templates when the user message should be passed through.
Command content is rendered like a chat template for the command turn; after a
slash command is invoked, `{{ chat.message }}` contains the user's request with
the command trigger stripped.

## Task

- `{{ task }}`
- `{{ task.id }}`
- `{{ task.title }}`
- `{{ task.instructions }}`
- `{{ task.labels }}`
- `{{ task.status }}`
- `{{ task.priority }}`
- `{{ task.slug }}`

Task variables belong in routine task execution templates, routine step prompts,
and gate evaluation prompts. Prefer `{{ task }}` for compact full context and
scalar fields when only one value is needed.

## Project

- `{{ project }}`
- `{{ project.name }}`
- `{{ project.slug }}`
- `{{ project.description }}`
- `{{ project.context }}`
- `{{ project.metadata }}`
- `{{ project.working_dir }}`

Projects provide workspace and execution context. Library knowledge is retrieved
through knowledge tools, not through an aggregate project-doc variable.

## Knowledge

- `{{ lib.<pack_slug> }}`
- `{{ pkg.nenjo_ai.packages.knowledge.core }}`

Knowledge variables render compact pack indexes. They are discovery hints, not
full source material. After seeing an index, use:

- `list_knowledge_packs`
- `search_knowledge`
- `list_knowledge_neighbors`
- `read_knowledge_doc`

Package-installed knowledge uses the
`pkg.<source_scope>.<source_repo>.<package_name>.<pack_name>` namespace. Library
knowledge uses `lib.<pack_slug>`.

When composing prompts, include the knowledge selector when the agent should
discover and read that source during execution. Do not paste the resolved
knowledge document body into prompt text.

## Routine

- `{{ routine }}`
- `{{ routine.slug }}`
- `{{ routine.name }}`
- `{{ routine.execution_id }}`
- `{{ routine.step.name }}`
- `{{ routine.step.type }}`
- `{{ routine.step.instructions }}`
- `{{ routine.step.metadata }}`
- `{{ routine.handoffs }}`

Use routine variables in routine step templates and workflow-aware prompts.
When present, `routine.handoffs` is the authoritative upstream input for the
current routine step, including gate steps.

## Git

- `{{ git }}`
- `{{ git.current_branch }}`
- `{{ git.target_branch }}`
- `{{ git.work_dir }}`
- `{{ git.repo_url }}`

Use Git variables when task execution depends on repository state or local
workspace paths.

## Memory And Artifacts

- `{{ memories }}`
- `{{ memories.core }}`
- `{{ memories.project }}`
- `{{ memories.shared }}`
- `{{ memory_profile }}`
- `{{ memory_profile.core_focus }}`
- `{{ memory_profile.project_focus }}`
- `{{ memory_profile.shared_focus }}`
- `{{ artifacts }}`
- `{{ artifacts.project }}`
- `{{ artifacts.workspace }}`

Memory is learned context. Artifacts are saved generated outputs or workspace
files. Library and package knowledge remain explicit source material retrieved
through knowledge tools.

## Context Blocks

Context blocks render by their refs, for example:

```jinja
{{ context.methodology }}
{{ pkg.nenjo_ai.packages.context.tools.tool_usage }}
{{ pkg.nenjo_ai.packages.context.operations.write_discipline }}
```

`pkg.*` is only used for package-installed context blocks and package knowledge.
Installed agents, abilities, domains, commands, MCP servers, script tools, and
routines are resolved as runtime resources, not prompt variables.

When composing prompts, include the context block selector. Do not paste the
resolved context block template into another prompt.

## Global

- `{{ global.timestamp }}`

Use this when the prompt needs the current timestamp.

## Pitfalls To Avoid

- Using variables that are not listed in this reference.
- Treating knowledge pack indexes as full source material.
- Copying resolved context or knowledge bodies instead of using selectors.
- Treating memory as Library knowledge.
- Embedding platform-scope assumptions in prompt templates.
