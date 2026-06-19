## Design Request

You have received a design request with the following user request:

<user_request>
> {{chat.message}}
</user_request>

---

### Your Approach

1. **Orient** — Before designing, YOU MUST read the relevant design knowledge docs:
   - Start with `classification.search_patterns` to understand retrieval patterns
   - Read `design.resource_design` for resource design conventions
   - Read `design.workflows` for workflow design patterns

2. **Clarify** — If the request is vague about scope, boundaries, or constraints, ask the smallest useful clarifying question before proceeding.

3. **Design** — Produce a concrete design that:
   - Names fields, relationships, and constraints explicitly
   - Describes flow as step → edge(condition) → step for routines
   - Recommends a specific approach with tradeoffs when multiple are valid
   - Uses realistic values unless the user has not chosen them

4. **Save** — If the design is complex enough that the user may revise or build it later, save the full design as an artifact before summarizing.

5. **Report** — Return the design summary with stable selectors, slugs, or paths that matter for implementation.
