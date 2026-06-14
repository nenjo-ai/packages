# Context Blocks — Reusable Prompt Context

## Purpose

Context blocks store durable, reusable prompt guidance. They define principles,
methodology, tool-use rules, routing patterns, and other operating behavior that
should be referenced across agents, abilities, domains, routines, and workflows
without duplicating text.

## What A Context Block Is

A context block contains:

- a clear purpose and description;
- a stable path-like reference;
- a template body injected into prompts.

Examples:

```jinja
{{ context.methodology }}
{{ pkg.nenjo_ai.packages.context.agents.sub_agents }}
{{ pkg.nenjo_ai.packages.context.tools.tool_usage }}
```

Use package context refs for package-installed context blocks. Use local context
refs such as `context.methodology` for resources authored in the active manifest
or platform environment.

## Selectors Over Copies

When composing an agent, ability, domain, routine, or another context block,
reference reusable context by selector. Do not copy the resolved context block
template into another prompt.

Selectors keep the prompt connected to the source block:

```jinja
{{ context.methodology }}
{{ pkg.nenjo_ai.packages.context.operations.write_discipline }}
```

Copying the template body creates a stale fork. Read context block metadata and
content only to understand which selector to use and whether the block fits the
resource being composed.

## Strong Uses

- Operating principles and methodology.
- Delegation and tool-use patterns.
- Review standards and quality criteria.
- Prompt-writing conventions.
- Domain-specific behavior guidance.

## Weak Uses

Do not use context blocks for:

- one-off user messages;
- task-specific details;
- source-of-truth Library knowledge;
- resource metadata that belongs on the resource;
- tool docs that should be represented by abilities or platform tool schemas.

## Tools

Use these tools for context block work:

- `list_context_blocks`
- `get_context_block`
- `configure_context_block`

Use `list_context_blocks` to discover existing slugs/selectors and
`get_context_block` to inspect the full ContextBlockDocument, including
template. Use `configure_context_block` for all context block writes: creation,
metadata changes, and template changes.

## Best Practices

- Keep context blocks focused and composable.
- Use stable, hierarchical refs such as `context.methodology` or
  `pkg.nenjo_ai.packages.context.operations.write_discipline`.
- Prefer a few durable blocks over many overlapping fragments.
- Version and review blocks when their behavior changes significantly.
