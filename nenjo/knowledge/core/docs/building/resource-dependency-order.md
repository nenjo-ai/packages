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
6. Create abilities and domains that reference existing tools and context.
7. Assign abilities/domains/tool refs to agents only after those refs exist.
8. Create councils before routines that reference those councils.
9. Create routines with valid agent, gate, council, and terminal refs.
10. Create projects before tasks and executions.
11. Create tasks with exactly one executor surface: assigned agent ref or routine
    ref.
12. Start, pause, resume, or inspect executions only after project and task refs
    are valid.

Packages are not in the platform write order for agents. Package manifests are
source-controlled install bundles and should be treated as reference material
unless the user is explicitly authoring package files in an SDK/local context.

## Tool Guidance

- Agent work: `create_agent`, `update_agent`, `update_agent_prompt`.
- Ability work: `create_ability`, `update_ability`, `update_ability_prompt`.
- Domain work: `create_domain`, `update_domain`, `update_domain_prompt`.
- Context work: `create_context_block`, `update_context_block`,
  `update_context_block_content`.
- Routine work: `create_routine`, `update_routine`.
- Council work: `create_council`, `update_council`, `update_council_member`.
- Project work: `create_project`, `update_project`.
- Task work: `create_project_tasks`, `update_project_task`.
- Execution work: `start_project_execution`, `pause_project_execution`,
  `resume_project_execution`.
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
