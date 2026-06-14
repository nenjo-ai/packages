# Prompt Design

## Purpose
Use prompt design when deciding what belongs in system prompts, developer prompts, templates, context blocks, memory, or retrieved knowledge.

Good prompt design keeps stable behavior separate from live state and retrieved evidence.

## Design Questions

- Which instructions are stable identity versus task-specific framing?
- Which context should be injected through template variables?
- Which facts should live in Library or package knowledge instead of prompts?
- Which reusable guidance belongs in context blocks?

## Prompt Placement

| Content | Best Location |
|---|---|
| Stable identity and principles | system prompt |
| Tool usage, reasoning style, operating rules | developer prompt |
| External response style and hidden intent classification | developer prompt or reusable context block |
| Mode-specific task framing | prompt template |
| Reusable snippets | context block |
| Project/user state | template variable or memory |
| Source material | Library or package knowledge pack |

## Minimum Prompt Design

Include:

- system prompt identity, role, principles, and durable boundaries;
- developer prompt operating loop, tool policy, retrieval policy, and verification rules;
- context block imports for reusable guidance;
- knowledge seeds for compact retrieval starting points;
- template variables needed for chat, task, gate, or heartbeat modes;
- memory guidance for what should be remembered;
- verification plan for rendered prompt sections and token cost.

## Boundary Rules

- Keep stable identity in the system prompt.
- Put operational guidance in developer prompt or context blocks.
- Use knowledge for reference material that should be discovered and traversed.
- Use memory for learned preferences, corrections, and project facts.
- Use templates for mode-specific framing rather than adding every mode to the core prompt.
- Prefer one-line runtime templates that include reusable context blocks when
  the framing should be generic across agents.
- Keep intent classification, routing, and retrieval planning internal unless
  the user asks to inspect the agent's decision process.

## Pitfalls

- Putting mutable project context into stable prompts.
- Duplicating Library or package knowledge inside prompts.
- Mixing chat, task, gate, and heartbeat behavior into one template.
- Seeding too many knowledge docs instead of giving the agent a compact index and
  retrieval instructions.
- Making templates print internal labels such as `Intent:` or `Route:` as part
  of normal replies.
- Treating system and developer prompt as separate. They are concatenated together when constructing the full agent prompt.

## Agent Guidance

Read this before authoring agent prompts. Use `building.prompt_structuring` and `building.template_vars` for exact construction details.
