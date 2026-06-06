# Script Tools — Script-Backed Runtime Tools

## Purpose

Script tools are native or package-provided tools backed by executable scripts.
They can be assigned to agents, abilities, or domains by slug/ref.

## Authoring And Assignment

Script tool manifests are usually provided by packages or controlled runtime
setup. Agents, abilities, and domains reference script tools through
`script_tools` assignment lists.

Do not imply a generic agent-write script-tool mutation tool unless the active
platform exposes one. Builder abilities may wire existing script tool refs when
the user explicitly requests it and the refs have been read or verified.

## Agent Guidance

- Read or verify available script tool refs before assigning them.
- Prefer narrow tools with clear input/output contracts.
- Treat script execution as sensitive because it can affect local workspaces.
- Report missing script tool refs instead of inventing them.
