# Knowledge Doc Kinds

## Purpose
Use this reference to choose `kind` values for knowledge documents.

The `kind` field is a user-defined string that categorizes the document kind or purpose inside a pack. It helps Nenji navigate source material, but it is not a platform-controlled enum and it is not a closed global classification system.

Kinds should describe the document's job, not its file location. Keep values stable within a pack so agents can combine `kind`, tags, summaries, and related edges reliably.

## Recommended Kinds

These values are pack conventions for Nenjo Core. Other knowledge packs may define their own values when their authors need different categories.

| Kind | Use For |
|---|---|
| `orientation` | High-level mental model and product boundaries |
| `design` | Recommendations, tradeoffs, and resource design guidance |
| `resource` | Concrete resource behavior, fields, and runtime semantics |
| `build` | Construction rules, manifests, template variables, and ordering |
| `classification` | Classification guides and decision tables |
| `reference` | Stable facts, conventions, glossaries, and lookup material |
| `policy` | Required rules or governance constraints |
| `entity` | Structured data model definitions |

## Agent Guidance

When selecting docs, treat `kind` as author-provided metadata. Combine it with tags, summaries, and graph relationships. Do not assume folder names define the document's purpose, and do not reject a document solely because its pack uses a custom `kind` value.
