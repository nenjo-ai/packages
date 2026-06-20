# Commands — User-Invoked Chat Templates

## Purpose

Commands are user-facing slash commands that replace the normal chat template
for a single chat turn. They let users invoke a named prompt workflow such as
`/design` without switching domains or asking an agent to infer the mode.

Use commands for explicit, lightweight prompt entrypoints. Use domains for
user-approved mode changes, abilities for agent-invoked specialist tools, and
routines for persistent multi-step workflow graphs.

## What A Command Is

A command:

- Has a unique slash trigger such as `/design`.
- May have a folder-like `path` used for UI grouping.
- Has Markdown `content` that acts as the chat template for the turn.
- Receives runtime template variables such as `{{ chat.message }}`.
- Is either platform-authored native content or package-installed file-backed
  content.

Root commands are ordinary slash commands such as `/design`. Do not model UI
grouping by inventing colon subcommands such as `/design:workflows`; use
`path` for grouping and keep the slash trigger stable.

## Core Fields

- `name` — Stable internal slug. Platform-authored commands derive this from
  the slash command unless a caller explicitly supplies it.
- `path` — Optional lowercase slash-separated grouping path for the UI command
  selector. Use `""` for root-level commands.
- `command` — The user-facing slash trigger. It must start with `/`.
- `description` — Short human-readable summary for selection and review.
- `content` — Markdown prompt template. Platform-authored commands use this for
  native command bodies. Package manifests may also use inline `content` for
  short source-managed commands.
- `content_path` — Preferred package manifest field for Nenjo-authored package
  commands with a separate Markdown file. It points to the command body, for example
  `nenjo/nenji/commands/design/command.md`.
- `entry_path` — Runtime/plugin compatibility field for a file path relative to
  `root_path`.
- `root_path` — Runtime/plugin compatibility field for the directory containing
  `entry_path`.
- `source_type` — `native` for platform-authored commands or `package` for
  installed package commands.
- `read_only` — Package-managed commands are read-only from the platform UI and
  agent write tools.

## Runtime Behavior

When a user sends a chat message that starts with a command trigger:

1. The worker resolves the installed command by exact slash command.
2. The command trigger is stripped from `{{ chat.message }}` so command content
   receives the user's actual request or arguments.
3. The command content replaces `prompt_config.templates.chat` for that turn.
4. The command template is rendered with the same template system as chat
   templates before the provider call.
5. The rendered command prompt is added to the model chat input.

The command does not permanently change the agent, domain, ability set, memory
profile, or platform permissions.

## Native And Package Commands

Platform-authored native commands store command content as managed inline
content. Metadata such as `command`, `path`, and `description` is not sensitive
by itself; command content can include sensitive prompt guidance.

Package-installed commands are source-managed. Their database rows contain
metadata and file paths, while the worker reads the Markdown body from the
installed package source. Nenjo-authored package manifests may use inline
`content` for short commands or `content_path` for separate Markdown files.
When `content_path` is used, the installer normalizes it to the
runtime-compatible `root_path` and `entry_path` fields. Claude/plugin commands
may still use `root_path` plus `entry_path` directly. Package commands are
read-only in the dashboard and through platform write tools; change them in the
package source and reinstall/update the package.

## Tools

Use `configure_command` for command writes.

On create, omit `command_ref`, include `metadata.command`, optional
`metadata.path`, optional `metadata.description`, and `content`. Omit
`metadata.name` unless preserving a specific existing internal slug is required;
the platform derives the internal name from the slash command.

On update, include `command_ref` with the current internal name or slash command
and provide only the metadata fields or content that should change.

## Pitfalls To Avoid

- Using commands to grant permissions or activate long-lived modes.
- Encoding grouping in colon command names instead of `path`.
- Editing package-managed commands through platform-native write tools.
- Copying full knowledge or context block bodies into command content when a
  stable selector should be used instead.
- Putting secrets into metadata fields; keep sensitive prompt guidance in
  command content.
