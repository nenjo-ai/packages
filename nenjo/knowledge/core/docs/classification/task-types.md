# Task Type And Priority Classification

## Purpose

Use this classification guide when classifying project tasks by work type, priority,
urgency, and complexity.

## Task Types

| Type | Meaning |
|---|---|
| `bug` | Correct broken or unintended behavior |
| `feature` | Add new user-visible or system capability |
| `task` | General work item that is not clearly bug or feature |
| `improvement` | Improve existing behavior, quality, performance, or maintainability |

## Priority

| Priority | Meaning |
|---|---|
| `critical` | Severe business, security, legal, or outage impact |
| `high` | Important work that materially blocks goals or users |
| `medium` | Normal planned work |
| `low` | Nice-to-have, polish, or low-risk cleanup |

## Urgency

| Urgency | Time Horizon |
|---|---|
| immediate | Hours |
| soon | Days |
| planned | Weeks |
| backlog | No current time pressure |

Urgency can inform `order_index`, scheduling decisions, and project planning, but
the task `priority` field should stay one of the supported priority values.

## Agent Guidance

When creating tasks, choose a valid `type`, `priority`, and optional
`complexity`. Do not invent unsupported type or priority values.
