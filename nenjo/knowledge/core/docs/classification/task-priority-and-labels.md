# Task Priority And Label Classification

## Purpose

Use priority and organization-owned labels to classify tasks. The task model
does not have fixed type, urgency, or complexity fields.

## Priority

| Priority | Meaning |
|---|---|
| `critical` | Severe business, security, legal, or outage impact |
| `high` | Important work that materially blocks goals or users |
| `medium` | Normal planned work |
| `low` | Nice-to-have, polish, or low-risk cleanup |

Choose the lowest priority that accurately represents impact. Priority is not a
workflow status and does not bypass the worker concurrency limit.

## Labels

Labels are organization-owned resources. The platform persists assignments by
`label_ids`, while `configure_task` accepts label names and resolves them at the
organization boundary. When creating a task, missing names are created in the
organization catalog. Updates require existing names. Use labels for
classifications that vary by organization, such as:

- work area (`frontend`, `platform`, `worker`);
- work intent (`bug`, `feature`, `maintenance`);
- operating concern (`security`, `performance`, `documentation`);
- planning horizon (`immediate`, `soon`, `planned`, `backlog`).

Inspect `list_task_labels` and reuse existing labels when possible. Matching is
trimmed and case-insensitive, so do not create spelling or casing variants. Do
not invent task fields or store sensitive instructions in label descriptions.

## Agent Guidance

When creating or updating a task:

1. choose a supported priority;
2. pass the complete desired label-name set to `configure_task`; omit `labels`
   to preserve assignments or pass `[]` to clear them;
3. put the actual objective, constraints, and completion guidance in
   `instructions`;
4. use the organization status row for workflow state.
