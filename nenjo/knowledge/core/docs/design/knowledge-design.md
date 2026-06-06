# Knowledge Design

## Purpose
Use knowledge design when deciding how to organize Library or package knowledge so agents can retrieve the right information quickly.

Knowledge documents should be small enough to select accurately and connected enough to support graph traversal.

## Design Questions

- What question should this doc answer?
- Is the doc meant for orientation, design, resource behavior, building rules, classification, or reference?
- Which tags help an agent find it before reading the body?
- Which related docs should be traversed before or after this doc?

## Good Knowledge Shape

A good knowledge doc has:

- one strong reason to exist;
- a stable selector;
- a user-defined `kind` value that categorizes the document's purpose;
- a summary that explains when to read it;
- sparse semantic tags;
- explicit related edges.

## Minimum Knowledge Design

Include:

- pack purpose and intended agent audience;
- selectors and source paths;
- user-defined doc kind for each document;
- summary that describes when to read the doc;
- tags for intent, resource, topic, surface, audience, or risk;
- related edges for dependency, reference, definition, and classification;
- seeded docs for the smallest useful starting index;
- verification plan for selector validity, graph traversal, and prompt rendering.

## Retrieval Patterns

- Use orientation docs to answer broad questions.
- Use design docs for recommendations and tradeoffs.
- Use resource docs for concrete behavior and fields.
- Use building docs for manifests, templates, and ordering.
- Use classification docs to choose a search or build path.
- Use reference docs for exact terms and conventions.

## Boundary Rules

- Use knowledge docs for searchable, traversable material.
- Use context blocks for instructions that must render in the prompt.
- Use summaries and tags to help agents select before reading full content.
- Prefer explicit related edges over asking agents to guess nearby docs.

## Agent Guidance

Use this doc when designing packs or deciding whether to split, merge, tag, or relate knowledge documents.
