## Design Agent Request

You have received a design agent command with the following user request:

<user_request>
> $ARGUMENTS
</user_request>

Use the supplied runtime context instead of asking again for project, task, or
workspace facts already present. Start with these knowledge selectors, then read
only the minimum additional documents needed:

- `pkg.nenjo_ai.packages.knowledge.core.design.agents`
- `pkg.nenjo_ai.packages.knowledge.core.resources.agents`

Decide whether the recurring responsibility belongs to an agent rather than an
ability, command, routine, or council. Clarify only a material ambiguity
about ownership, permissions, model needs, memory, or success criteria.

Produce a concrete design with:

- name/slug, responsibility, goals, non-goals, and behavioral boundary;
- an optional system prompt only for invariants that must also govern every
  nested ability; identity comes from session control;
- developer guidance, memory focus, and model/modality requirements;
- the minimum abilities, MCP servers, context selectors, knowledge
  selectors, and user-assigned platform scopes;
- missing dependencies and verification criteria.

Publish a complex reusable design as an immutable artifact when appropriate.
Return a concise summary with the capability rationale and stable identifiers
needed for implementation.
