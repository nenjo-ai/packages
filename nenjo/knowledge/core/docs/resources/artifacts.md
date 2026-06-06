# Artifacts — Saved Outputs

## Purpose

Artifacts are saved generated outputs or workspace files. They are indexed in
prompts through artifact variables and are separate from memory, Library
knowledge, and package source material.

## Runtime Variables

- `{{ artifacts }}`
- `{{ artifacts.project }}`
- `{{ artifacts.workspace }}`

Use artifacts when a prompt needs to see saved outputs or workspace deliverables
from previous work.

## Agent Guidance

- Save substantial intermediate designs or generated deliverables as artifacts
  when they may be reused.
- Do not treat artifacts as source-of-truth Library knowledge.
- Do not treat artifacts as learned memory.
