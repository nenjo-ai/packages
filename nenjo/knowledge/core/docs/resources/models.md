# Models — LLM Configuration Refs

## Purpose

Models define the LLM configuration refs agents can use. Agent resources point
at model slugs/refs; agents should read available models before selecting one.

## Tools

Use these platform tools for model work:

- `list_models`
- `get_model`
- `create_model`
- `update_model`
- `delete_model`

Model creation and mutation are platform write operations. If the active agent
does not have the model write surface, report the missing scope family and stop.

## Agent Guidance

- Prefer existing model refs when building agents.
- Read the selected model before assigning it.
- Do not invent model refs that are not present in platform state.
