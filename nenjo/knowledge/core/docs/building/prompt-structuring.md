# Prompt Structuring

## Purpose

Use this guide when composing agent, ability, domain, routine, gate, heartbeat,
and context-block prompts.

Good prompts separate stable behavior from runtime context and source material.
They seed only the docs needed for discovery, then require tools for current
state and full source reads.

## Prompt Layers

1. System prompt — stable identity, durable principles, and non-negotiable role
   boundaries.
2. Developer prompt — routing rules, tool-use expectations, and tactical
   behavior.
3. Runtime template — chat, task, gate, routine, heartbeat, memory,
   artifact, project, and Git variables.
4. Evidence gathered during the turn — tool results, selected Library/package
   knowledge, memory recall, and file reads.

Do not put current mutable platform state or full source material into stable
prompts. Read current state and selected source docs through tools.

## Referenced Source Material

Prompts should reference reusable source material by stable selector, not by
copying resolved bodies.

Use context-block selectors for reusable behavior:

```jinja
{{ context.methodology }}
{{ pkg.nenjo_ai.packages.context.tools.tool_usage }}
```

Use knowledge-pack selectors as discovery indexes, then require knowledge tools
to read selected full documents during execution:

```jinja
{{ lib.<pack_slug> }}
{{ pkg.nenjo_ai.packages.knowledge.core }}
```

Do not paste Library docs, package knowledge docs, or context block templates
into stable prompts. Read them to choose the right selector and to write correct
routing instructions.

## What Goes Where

| Content | Best Home |
|---|---|
| Stable identity and principles | system prompt |
| Tool-use discipline and routing | developer prompt or context block |
| External response style and hidden intent classification | developer prompt or context block |
| Task or chat data | runtime template |
| Reusable prompt behavior | context block |
| Learned preferences and corrections | memory |
| Source material and policies | Library or package knowledge |
| Generated files and outputs | artifacts |
| Platform permissions | platform scopes assigned by users/platform |

## Runtime Variables

Runtime templates should be thin. Prefer a one-line include of a reusable
runtime context block, such as
`{{ pkg.nenjo_ai.packages.context.runtime.task_execution }}`, when the same
framing should work across agents. That context block should own the current
mode's input variables and runtime instructions. It may remind the agent to
classify intent internally for routing, retrieval, or tool selection, but it
should not require visible headings such as `Intent:`, `Route:`, or
`Classification:` in normal user-facing output.

Use only variables defined in `building.template_vars`. Common high-value
variables include:

- `{{ chat.message }}`
- `{{ task }}`
- `{{ task.acceptance_criteria }}`
- `{{ project }}`
- `{{ project.working_dir }}`
- `{{ routine }}`
- `{{ routine.step.instructions }}`
- `{{ gate.previous_output }}`
- `{{ memories }}`
- `{{ artifacts }}`
- `{{ git }}`

Use only variables documented in `building.template_vars`. Available tools and
resources should come from the platform tool surface and live reads, not invented
template variables.

## Knowledge Retrieval Pattern

When a prompt exposes `{{ pkg.nenjo_ai.packages.knowledge.core }}` or a Library
pack variable:

1. Search metadata for likely selectors.
2. Read the most specific doc.
3. Traverse outbound neighbors for dependencies and exact rules.
4. Read selected full docs.
5. Answer from the docs actually read.

For Library source material, prefer `search_knowledge`,
`list_knowledge_neighbors`, and `read_knowledge_doc` over memory.

## Build Prompt Pattern

Builder prompts should:

- state the resource family they own;
- require live reads before writes;
- require referenced knowledge and context docs to be read before composing prompt_config;
- name the exact write tools they may use;
- forbid platform-scope assignment by agents;
- require readback verification;
- verify generated prompt_config contains intended selectors rather than copied source bodies;
- report slugs, refs, and selectors.

Example:

```jinja
<knowledge>
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
{{ pkg.nenjo_ai.packages.knowledge.core.building.resource_dependency_order }}
{{ pkg.nenjo_ai.packages.context.knowledge.knowledge_routing }}
</knowledge>

<rules>
- Read current agent state before mutation.
- Read referenced context block and knowledge docs before composing prompt_config.
- Use configure_agent for metadata, prompt_config, and assignment changes.
- Use selectors for reusable context and knowledge instead of copying source bodies.
- Do not assign platform scopes.
- Verify by reading back the agent by slug/ref.
</rules>
```

## Gate Prompt Pattern

Gate prompts should combine:

- `{{ gate.previous_output }}`;
- `{{ task.acceptance_criteria }}`;
- routine step instructions or explicit prompt text;
- required output schema for pass/fail decisions.

Use task acceptance criteria, routine step instructions, or explicit prompt text
for gate criteria.

## Pitfalls To Avoid

- Seeding too many docs instead of using retrieval tools.
- Treating knowledge indexes as full source material.
- Copying context block templates or knowledge doc bodies into stable prompts.
- Treating memory as Library documentation.
- Embedding platform-scope changes in prompts.
- Using variables that are not rendered by the current runtime.
- Leaking internal routing, intent classification, or phase labels into normal
  replies.
