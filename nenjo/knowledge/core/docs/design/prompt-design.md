# Prompt Design

## Purpose
Use prompt design when deciding what belongs in system prompts, developer prompts, context blocks, runtime context, memory, or retrieved knowledge.

Good prompt design keeps stable behavior separate from live state and retrieved evidence.

## Design Questions

- Which rules must apply to both the main agent and every nested ability?
- Which live fields belong in typed session or turn context?
- Which facts should live in Library or package knowledge instead of prompts?
- Which reusable guidance belongs in context blocks?

## Prompt Placement

| Content | Best Location |
|---|---|
| Executing identity and description | runtime session control |
| Universal inherited invariants | optional system prompt |
| Role behavior, tool usage, and operating rules | developer prompt |
| External response style and hidden intent classification | developer prompt or reusable context block |
| Mode-specific task data | runtime-owned turn context and raw input |
| Reusable snippets | context block |
| Project/user state | runtime session context or memory |
| Source material | Library or package knowledge pack |

## Minimum Prompt Design

Include only what is needed:

- an optional system prompt for invariants that must survive nested ability execution;
- developer prompt role behavior, tool policy, retrieval policy, and verification rules;
- context block imports for reusable guidance;
- knowledge seeds for compact retrieval starting points;
- memory guidance for what should be remembered;
- verification plan for rendered prompt sections and token cost.

## Boundary Rules

- Do not repeat executing identity; session control supplies it.
- Keep the system prompt empty unless a rule must be inherited by abilities.
- Put role and operational guidance in the developer prompt or context blocks.
- Use knowledge for reference material that should be discovered and traversed.
- Use memory for learned preferences, corrections, and project facts.
- Keep mode-specific data in runtime-owned context instead of the static prompt.
- Keep intent classification, routing, and retrieval planning internal unless
  the user asks to inspect the agent's decision process.

## Pitfalls

- Putting mutable project context into stable prompts.
- Duplicating Library or package knowledge inside prompts.
- Copying runtime-owned identity or execution state into agent configuration.
- Seeding too many knowledge docs instead of giving the agent a compact index and
  retrieval instructions.
- Making prompts print internal labels such as `Intent:` or `Route:` as part
  of normal replies.
- Treating system and developer as equivalent authoring surfaces. They currently
  share one provider instruction message, but nested abilities inherit only the
  system portion and replace developer guidance.

## Agent Guidance

Read this before authoring agent prompts. Use `building.prompt_structuring` and
`building.prompt_context` for exact construction details.
