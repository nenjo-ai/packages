# Command Design

## Purpose

Use command design when a user needs an explicit slash-triggered prompt workflow
inside chat.

Commands are best for recognizable entrypoints that reshape one chat turn, such
as design requests, audits, summaries, or report formats. A command should be
easy for a user to choose and should not require the agent to infer the mode.

## Design Questions

- What exact slash trigger should the user type?
- Should the command appear at the root level or under a UI grouping `path`?
- What user request or arguments should be passed through via
  `{{ chat.message }}`?
- Which reusable context or knowledge selectors should the command seed?
- Does the command only reshape the current turn, or does it actually require a
  domain, ability, or routine?
- Is the command native platform-authored content or package-installed
  source-managed content?

## Good Command Shape

A good command has:

- a short unique slash trigger;
- a concise description;
- a clear Markdown content template;
- minimal seeded knowledge or context;
- explicit output expectations;
- no hidden permission change or persistent mode switch.

## Minimum Command Design

Include:

- slash trigger, for example `/design`;
- UI grouping path, or `""` for root-level;
- description;
- command content template;
- expected runtime variables, usually `{{ chat.message }}`;
- source owner: native platform command or package command;
- package content source when source-managed: inline `content` for short
  commands or `content_path` for separate Markdown files;
- verification plan: command appears in selector and renders the intended prompt
  for a representative user message.

## Use A Command For

- user-invoked prompt workflows;
- consistent design, review, audit, or summary formats;
- thin routing templates that seed knowledge and tell the agent how to answer;
- package-distributed prompt entrypoints.

## Avoid A Command For

- durable role identity;
- explicit privilege or mode changes;
- agent-invoked specialist work;
- multi-step auditable execution graphs;
- project task state or scheduling.

## Boundary Rules

- If the user explicitly invokes the behavior with `/...`, use a command.
- If the agent should decide when to invoke the behavior, use an ability.
- If the user must approve a long-lived mode or capability change, use a domain.
- If the work has persistent steps, gates, retries, or schedules, use a routine.
- If the behavior should define who performs work over time, use an agent.

## Agent Guidance

Read `resources.commands` for runtime semantics and package/native storage.
Read `building.template_vars` before choosing variables. Read
`building.prompt_structuring` before writing command content that references
context blocks or knowledge.
