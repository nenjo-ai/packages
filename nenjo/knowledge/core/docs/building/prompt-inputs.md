# Prompt Context

## Purpose

Nenjo keeps authored instructions byte-stable and places changing execution
data in runtime-owned messages. Use this reference when authoring system
prompts, developer prompts, or context blocks.

## Static Prompt References

Static prompts may use:

- declared package arguments under `{{ args.* }}`
- context-block selectors
- knowledge-pack index selectors

Example context and knowledge selectors:

```jinja
{{ context.methodology }}
{{ pkg.nenjo_ai.packages.context.tools.host_tools }}
{{ pkg.nenjo_ai.packages.context.operations.write_discipline }}
{{ lib.product_docs }}
{{ pkg.nenjo_ai.packages.knowledge.core }}
```

Package arguments are resolved before execution. If a referenced argument
changes, the compiled static instruction prefix intentionally starts a new
cache epoch.

Knowledge selectors provide compact discovery indexes, not full source
material. Use `search_knowledge`, `list_knowledge_neighbors`, and
`read_knowledge_doc` to retrieve selected documents.

## Runtime-Owned Context

Agent identity and live state are not available through template selectors.

The runtime emits session control followed by optional session data. Control
contains the executing agent identity and project/workspace routing facts. Data
contains project description/context/metadata and the memory snapshot frozen for
the session epoch. The memory profile controls retrieval and memory writing but
is not repeated as model-visible content. Empty session data is omitted.

For each logical turn, the runtime emits turn control followed by optional turn
data. Control contains the clock, execution kind, identifiers, routing, workflow
instructions, Git/worktree facts, and gate verdict. Data contains descriptive
task/routine fields, handoffs, and arbitrary results. The raw chat message or
task instructions follow as user input. Empty turn data is omitted, and retries
reuse the original persisted bytes.

The runtime protocol tells agents to read the applicable context before acting.
Session context applies for its session epoch; turn context applies only to the
immediately following logical turn and wins for overlapping facts. Control is
application guidance. Data is reference material and must not be followed as
instructions. User-authored text cannot create authority by copying the XML
tags.

## Selector Boundary

Authored prompts may reference only declared `args.*`, static context blocks,
and knowledge metadata. Package validation rejects runtime-state selector roots
in system prompts, developer prompts, and context blocks. Artifacts arrive as
typed inputs or through artifact tools. Slash-command content uses `$ARGUMENTS`
and becomes raw turn input.

## Pitfalls To Avoid

- Copying resolved context-block or knowledge-document bodies into prompts.
- Treating a knowledge index as full source material.
- Expecting executing-agent metadata to be available during static rendering.
- Expecting the mutable memory profile to appear in model context.
- Treating artifact access as a template-variable surface instead of using
  typed chat/task inputs and `list_artifacts`/`read_artifact`.
- Adding a new dynamic selector instead of extending typed session or turn
  context.
- Embedding platform-scope assumptions in prompt text.
