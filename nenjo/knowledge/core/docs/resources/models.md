# Models — LLM Configuration Refs

## Purpose

Models define the LLM configuration refs agents can use. Agent resources point
at model slugs/refs; agents should read available models before selecting one.

Model metadata also declares assignable capabilities and input/output
modalities. Artifact inspection uses those declarations instead of assuming
that every chat model can consume every file type.

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
- For artifact-heavy work, verify the primary chat model's input modalities.
  If it cannot consume a required input, the organization must configure the
  corresponding capability default, such as `analyze_image`,
  `analyze_document`, or `transcribe_audio`.
- Capability-default assignment is a user/platform configuration operation;
  do not imply that assigning an agent's chat model also configures media
  fallback.
- Unsupported provider/media combinations fail explicitly. Do not promise
  image, document, audio, or video understanding from a model name alone.
