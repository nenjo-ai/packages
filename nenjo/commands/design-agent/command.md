## Design Agent Request

You have received a design agent command with the following user request:

<user_request>
> {{chat.message}}
</user_request>

These are the relevant seeded knowledge docs that you MUST read first using the knowledge tools.
<seeded_knowledge>
{{ pkg.nenjo_ai.packages.knowledge.core.classification.search_patterns }}
{{ pkg.nenjo_ai.packages.knowledge.core.design.agents }}
{{ pkg.nenjo_ai.packages.knowledge.core.resources.agents }}
{{ pkg.nenjo_ai.packages.knowledge.core.design.prompts }}
{{ pkg.nenjo_ai.packages.knowledge.core.building.prompt_structuring }}
</seeded_knowledge>

---

### Your Approach

1. **Orient** - Understand the agent's recurring job before composing anything:
   - Classify the intended responsibility, user audience, operating mode, and non-goals.
   - Decide whether the request should be an agent or whether an ability, domain, routine, council, or command is the better primitive.
   - Identify what the agent must know, remember, retrieve, delegate, call, or write.
   - Read neighboring docs when the design depends on specific abilities, domains, context blocks, models, MCP servers, script tools, or workflows.

2. **Clarify** - If the request is vague about ownership, permissions, model needs, memory behavior, write authority, or success criteria, ask the smallest useful clarifying question before proceeding.

3. **Compose** - Produce a concrete agent design that:
   - Names the agent slug/display name and its durable responsibility.
   - Defines explicit goals, non-goals, and behavioral boundaries.
   - Separates stable system prompt identity from tactical developer guidance.
   - Specifies memory profile focus areas and what should not be remembered.
   - Recommends model requirements or a concrete model slug when appropriate.
   - Lists assigned abilities, domains, MCP servers, context selectors, and seeded knowledge selectors, with why each belongs.
   - States platform scopes needed for the intended work and flags scopes that should require a domain or user approval.
   - Names any missing dependencies that must be created before the agent can be built.

4. **Save** - If the design is complex enough that the user may revise, approve, or build it later, save the full design as an artifact before summarizing.

5. **Report** - Return a concise agent design summary that makes the implementation legible:
   - List the proposed agent identity, responsibility, and boundaries.
   - Call out the capability surface and why each assignment is included.
   - Include stable selectors, slugs, refs, or paths that matter for the next build step.
