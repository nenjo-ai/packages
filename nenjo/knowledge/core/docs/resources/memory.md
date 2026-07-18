# Memory — Scoped Agent Memory

## Purpose

Memory stores learned facts, preferences, corrections, and durable operating
context. Memory is not the Library, and it is not an artifact store. Library
knowledge is explicit source material. Artifacts are saved files or generated
outputs indexed separately in prompts.

## The Three Memory Scopes

| Scope | What It Stores | Visibility | Typical Content |
|---|---|---|---|
| `core_focus` | Long-term cross-project knowledge | Global to the agent | Methodology, durable preferences, corrections |
| `project_focus` | Project-specific learned context | Isolated per project | Task decisions, completion guidance, project conventions |
| `shared_focus` | Reusable knowledge shared across work | Shared when enabled | Team conventions, reusable patterns |

## Memory Profiles

A `memory_profile` is attached to an agent and describes what should be recalled
or saved in each scope. In manifests and prompt context it is structured data,
usually authored as YAML arrays.

```yaml
memory_profile:
  core_focus:
    - user corrections
    - methodology
  project_focus:
    - task decisions
    - task completion guidance
  shared_focus:
    - reusable patterns
    - team conventions
```

## Runtime Behavior

- Memory is scoped according to the active profile and project context.
- `project_focus` memory stays project-specific.
- `core_focus` and `shared_focus` are available only when configured.
- Memory is injected through `{{ memories }}`, `{{ memories.core }}`,
  `{{ memories.project }}`, and `{{ memories.shared }}`.
- Artifact indexes are injected separately through `{{ artifacts }}`,
  `{{ artifacts.project }}`, and `{{ artifacts.workspace }}`.
- Library knowledge is retrieved with knowledge tools, not memory variables.

## Pitfalls To Avoid

- Putting project-specific data into `core_focus`.
- Treating Library knowledge or artifacts as memory.
- Leaving memory profiles empty on long-running agents.
- Remembering stale facts instead of reading current platform or Library state.
