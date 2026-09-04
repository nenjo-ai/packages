# Resource Dependency Order

## Purpose

Use this when building or updating Nenjo resources so upstream refs exist before
downstream resources are wired.

## Recommended Order

1. Read current platform state and available tools.
2. Confirm user-controlled platform scopes are already present when needed.
3. Create or select models, MCP servers, script tools, and context blocks.
4. Create or update agents without broad assignments first.
5. Apply prompt updates after the agent exists and has been read back by slug/ref.
6. Create abilities that reference existing tools and context.
7. Create native commands after their content and retrieval requirements are
   known.
8. Assign ability and tool refs to agents only after those refs exist.
9. Create councils before routines that reference those councils.
10. Create routines with valid agent, gate, council, and terminal refs.
11. Create projects before tasks and executions.
12. Create tasks with exactly one executor surface: assigned agent ref or routine
    ref.
13. Dispatch, watch, cancel, retry, or inspect executions only after project and
    task refs are valid.

Packages are not in the platform write order for agents. Package manifests are
source-controlled install bundles and should be treated as reference material
unless the user is explicitly authoring package files in an SDK/local context.

## Tool Guidance

- Agent work: `list_agents`, `get_agent`, `configure_agent`.
- Ability work: `list_abilities`, `get_ability`, `configure_ability`.
- Command work: `list_commands`, `get_command`, `configure_command`.
- Context work: `list_context_blocks`, `get_context_block`,
  `configure_context_block`.
- Routine work: `list_routines`, `get_routine`, `configure_routine`.
- Council work: `list_councils`, `get_council`, `create_council`,
  `update_council`, `add_council_member`, `update_council_member`,
  `remove_council_member`, `delete_council`.
- Project work: `list_projects`, `get_project`, `create_project`,
  `update_project`, `delete_project`.
- Task work: `configure_task`, `delete_task`, `dispatch_task`.
- Execution work: `list_task_execution_runs`, `watch_execution_run`,
  `cancel_execution_run`, `retry_execution_run`.
- Library pack work: `create_knowledge_pack`, `update_knowledge_pack`.
- Library doc work: `create_knowledge_doc`, `update_knowledge_doc`,
  `delete_knowledge_doc`.

## Rules

- Read before writing.
- Verify every write by reading back the changed resource.
- Use slugs/refs/selectors in reports.
- Do not assign platform scopes from agent-authored resource writes.
- Do not delete Library knowledge packs from agent tools; pack removal is a platform delete operation.
- Stop and report the missing upstream ref or scope family when blocked.
