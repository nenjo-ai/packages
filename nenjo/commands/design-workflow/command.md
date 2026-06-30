## Design Workflow Request

You have received a design workflow command with the following user request:

<user_request>
> {{chat.message}}
</user_request>

These are the relevant seeded knowledge docs that you MUST read first using the knowledge tools.
<seeded_knowledge>
{{ pkg.nenjo_ai.packages.knowledge.core.classification.search_patterns }}
{{ pkg.nenjo_ai.packages.knowledge.core.design.workflows }}
{{ pkg.nenjo_ai.packages.knowledge.core.classification.workflow_patterns }}
{{ pkg.nenjo_ai.packages.knowledge.core.building.workflow_pattern_cookbook }}
{{ pkg.nenjo_ai.packages.knowledge.core.building.routine_flow_authoring }}
</seeded_knowledge>

---

### Your Approach

1. **Orient** - Understand the user's intent and the Nenjo primitives involved before composing anything:
   - Classify what is being designed: resource type(s), workflow shape, or cross-resource topology.
   - Start with `classification.search_patterns` to choose retrieval paths.
   - Traverse neighboring documents from those seeds; use the request intent as traversal direction cues.
   - Identify which primitives apply and how they relate.

2. **Clarify** - If the request is vague about scope, boundaries, constraints, or success criteria, ask the smallest useful clarifying question before proceeding.

3. **Compose** - Produce a concrete design or workflow composition that:
   - Names the chosen primitives and why each was selected over alternatives.
   - Names fields, relationships, and constraints explicitly.
   - Reads `building.routine_flow_authoring` before recommending concrete
     routine topology, edge metadata, handoff schemas, fan-out, joins, or gate retries.
   - Describes flow as step -> edge(condition) -> step for routines; name owners, dependencies, gates, edge handoff contracts, and terminal outcomes for workflows.
   - Recommends a specific approach with tradeoffs when multiple are valid.
   - Uses realistic values unless the user has not chosen them.

4. **Save** - If the design is complex enough that the user may revise, approve, or build it later, save the full design as an artifact before summarizing.

5. **Report** - Return a concise design summary that makes the composition legible:
   - List the primitives and components used and how they connect.
   - Call out key relationships, dependencies, and boundaries.
   - Include stable selectors, slugs, refs, or paths that matter for implementation or the next build step.
