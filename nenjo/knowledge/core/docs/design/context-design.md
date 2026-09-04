# Context Design

## Purpose
Use context design when deciding how reusable context should be modeled and injected into prompts.

Context blocks are best for reusable, authored context that is stable enough to version but too specific to live in a general prompt.

## Design Questions

- Is this context reusable across agents, tasks, or modes?
- Is it static authored guidance, or does it belong in runtime context?
- Does it depend only on declared package arguments or other static blocks?
- Should it be a context block, memory, knowledge doc, or prompt text?

## Good Context Shape

A good context block:

- has a clear selector or template name;
- contains focused guidance or source text;
- avoids hidden state;
- composes cleanly with agent prompts;
- stays distinct from memory and knowledge retrieval.

## Minimum Context Design

Include:

- selector or import path;
- intended consumers such as agents, abilities, or packages;
- stable instructions or source text to inject;
- required static package arguments, if any;
- where it should render in the prompt;
- related Library or package knowledge when the context tells agents how to retrieve;
- verification plan for prompt rendering and token cost.

## Boundary Rules

- Use context blocks for stable authored guidance that should be injected.
- Use Library or package knowledge for searchable reference or design material.
- Use memory for learned user/project facts and corrections.
- Use typed session or turn context for live runtime state.
- Use session control for identity, an optional system prompt for invariants
  inherited by abilities, and developer prompts for role behavior.

## Pitfalls

- Putting volatile runtime data in authored context.
- Using context blocks as unstructured storage.
- Duplicating Library or package knowledge in context blocks.
- Importing broad context into every prompt when a narrower ability context
  would be cheaper.

## Agent Guidance

Use this before creating reusable prompt context. Read `resources.context_blocks`
for resource behavior and `building.prompt_context` for static/runtime placement.
