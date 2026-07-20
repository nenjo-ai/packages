# Nenjo Capabilities

The `capabilities` package provides the official reusable Nenjo abilities for
building and managing platform resources. It can be installed independently or
pulled transitively by the `nenji` package.

It includes abilities for agents, abilities, commands, domains, context blocks,
Library knowledge, councils, routines, and tasks. The abilities depend on the
shared `context` and `knowledge` packages for their prompt guidance.

Package consumers assign abilities with stable, versionless logical refs. For
example:

```text
pkg:@nenjo-ai/packages:capabilities:ability:build-agent
pkg:@nenjo-ai/packages:capabilities:ability:build-routine
pkg:@nenjo-ai/packages:capabilities:ability:manage-tasks
```

The authored slug is the final component of each ref. Package versions remain
part of installation and runtime storage identity, but are intentionally absent
from agent-visible logical refs.
