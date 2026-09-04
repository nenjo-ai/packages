# Prompt Structuring

## Purpose

Use this guide when composing agent, ability, command, routine, gate,
and context-block prompts.

Good prompts separate stable behavior from runtime context and source material.
They start from runtime-provided facts, seed only the metadata needed for
discovery, and use tools only for missing or fresher state and selected source
reads.

## Prompt Layers

1. Optional system prompt — only durable invariants that must also govern every
   nested ability. Agent identity does not belong here.
2. Developer prompt — role behavior, routing, tool use, and response policy.
3. Runtime-owned session control plus optional session data.
4. Prior conversation history.
5. Runtime-owned turn control plus optional turn data, followed by raw input.
6. Evidence gathered during the turn — tool results, selected Library/package
   knowledge, and file reads.

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

Prefer specific knowledge-document selectors as discovery entries, then use
knowledge tools to read selected full documents during execution:

```jinja
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
```

A whole-pack selector such as `\{{ lib.<pack_slug> }}` or
`\{{ pkg.nenjo_ai.packages.knowledge.core }}` renders metadata for every
document and relation. Use one only when the agent repeatedly needs broad pack
discovery and its prompt budget permits it.

Do not paste Library docs, package knowledge docs, or context block templates
into stable prompts. Read them to choose the right selector and to write correct
routing instructions.

## What Goes Where

| Content | Best Home |
|---|---|
| Executing agent identity and description | runtime session control |
| Universal invariants inherited by abilities | optional system prompt |
| Tool-use discipline and routing | developer prompt or context block |
| External response style and hidden intent classification | developer prompt or context block |
| Explicit one-turn slash workflow | command content |
| Project/workspace routing facts | session control |
| Project descriptions and frozen memory | optional session data |
| Execution routing and workflow instructions | turn control |
| Descriptive task/routine data and results | optional turn data |
| Current task or chat request | raw input following turn context |
| Reusable prompt behavior | context block |
| Learned preferences and corrections | memory |
| Source material and policies | Library or package knowledge |
| Generated files and outputs | artifacts |
| Platform permissions | platform scopes assigned by users/platform |

## Runtime Context

Do not author live execution selectors. The worker owns canonical session and
turn serialization so identity, tasks, projects, routines, gates, Git state,
clocks, and memory do not alter the static instruction prefix. A missing live
field requires a runtime contract change, not a package prompt variable.

Session and turn control are application guidance; their optional data messages
are reference material. Empty data messages are omitted. Prompts should use the
provided context before issuing reads or asking the user to repeat it, while
still reading mutable resources when freshness matters.

Static prompts may use declared `args.*` package arguments, context-block
selectors, and knowledge selectors documented in `building.prompt_context`.
Available tools and resources come from the platform surface, not invented
template variables.

Artifacts arrive as typed chat/task inputs or through `list_artifacts` and
`read_artifact`; they are not injected through an aggregate prompt variable.

## Knowledge Retrieval Pattern

When a prompt exposes knowledge metadata:

1. Select the most specific matching seed.
2. Search metadata only when seeds are insufficient.
3. Traverse only edges that can supply a needed dependency or exact rule.
4. Read the minimum selected full docs.
5. Stop when the evidence is sufficient.

For Library source material, prefer `search_knowledge`,
`list_knowledge_neighbors`, and `read_knowledge_doc` over memory.

## Build Prompt Pattern

Builder prompts should:

- state the resource family they own;
- start from runtime context and require only the mutable reads needed before writes;
- select the minimum relevant knowledge/context docs before composing prompt_config;
- name the exact write tools they may use;
- forbid platform-scope assignment by agents;
- require readback verification;
- verify generated prompt_config contains intended selectors rather than copied source bodies;
- report slugs, refs, and selectors.

For agent builders, leave `system_prompt` empty unless a rule must govern both
the main agent and every nested ability. Identity is already in session control;
ordinary role and operating behavior belongs in `developer_prompt`.

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
