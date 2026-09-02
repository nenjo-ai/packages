# Prompt Structuring

## Purpose

Use this guide when composing agent, ability, domain, command, routine, gate,
and context-block prompts.

Good prompts separate stable behavior from runtime context and source material.
They seed only the docs needed for discovery, then require tools for current
state and full source reads.

## Prompt Layers

1. System prompt — stable identity, durable principles, and non-negotiable role
   boundaries.
2. Developer prompt — routing rules, tool-use expectations, and tactical
   behavior.
3. Runtime-owned context — a frozen session snapshot and a per-logical-turn
   snapshot, followed by raw chat or task input.
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
{{ pkg.nenjo_ai.packages.context.tools.host_tools }}
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
| Explicit one-turn slash workflow | command content |
| Task or chat data | runtime-owned turn context and raw input |
| Reusable prompt behavior | context block |
| Learned preferences and corrections | memory |
| Source material and policies | Library or package knowledge |
| Generated files and outputs | artifacts |
| Platform permissions | platform scopes assigned by users/platform |

## Runtime Context

Do not author live execution selectors. The worker owns canonical session and
turn serialization so tasks, projects, routines, gates, Git state, clocks, and
memory do not alter the static instruction prefix. Extend the typed runtime
context when a new live field is truly required.

The runtime places executing-agent identity in session control context. Static
prompts may use declared `args.*` package arguments, context-block selectors,
and knowledge-pack selectors documented in `building.template_vars`. Available
tools and resources should come from the platform tool surface and live reads,
not invented template variables.

Artifacts arrive as typed chat/task inputs or through `list_artifacts` and
`read_artifact`; they are not injected through an aggregate prompt variable.

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

Command content uses `$ARGUMENTS` and becomes raw user input. Keep it focused
and avoid copying full source documents into the command body.

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
- Use flat configure_agent fields for authored data, prompt_config, and assignment changes; always identify the agent by its stable top-level slug.
- Use selectors for reusable context and knowledge instead of copying source bodies.
- Do not assign platform scopes.
- Verify by reading back the agent by slug/ref.
</rules>
```

## Gate Behavior Pattern

The runtime supplies routine handoffs, task acceptance criteria, prior output,
and gate metadata in typed turn context. Stable gate operating rules belong in
the developer prompt; one-off evaluation instructions remain raw input.

Put completion guidance in task instructions, routine step instructions, or
explicit gate prompt text. Gates should call `route_next_steps` as their final action
with a pass or fail verdict and schema-valid handoffs for any activated
downstream routes.

## Pitfalls To Avoid

- Seeding too many docs instead of using retrieval tools.
- Treating knowledge indexes as full source material.
- Copying context block templates or knowledge doc bodies into stable prompts.
- Treating memory as Library documentation.
- Embedding platform-scope changes in prompts.
- Adding dynamic selectors instead of extending typed runtime context.
- Leaking internal routing, intent classification, or phase labels into normal
  replies.
