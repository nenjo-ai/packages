## Design Workflow Request

You have received a design workflow command with the following user request:

<user_request>
> $ARGUMENTS
</user_request>

Use the supplied runtime context instead of asking again for project, task, or
workspace facts already present. Begin with
`pkg.nenjo_ai.packages.knowledge.core.design.workflows`. Read
`building.routine_flow_authoring` only when the design needs concrete routine
topology or edge contracts; search or traverse to other docs only as required.

Clarify only a material ambiguity about scope, ownership, constraints, or success
criteria. Produce a concrete design that:

- chooses the minimum Nenjo primitives and explains why they fit;
- names owners, dependencies, fields, relationships, gates, and terminal outcomes;
- expresses routine flow as `step -> edge(condition) -> step`;
- defines handoff purpose and schema when data crosses an agent or gate edge;
- recommends one approach and states meaningful tradeoffs.

Publish a complex reusable design as an immutable artifact when appropriate.
Return a concise summary of the topology, boundaries, dependencies, and stable
identifiers needed for implementation.
