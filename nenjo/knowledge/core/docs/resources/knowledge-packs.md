# Knowledge Packs — Searchable Source Material

## Purpose

Knowledge packs expose explicit source material through a metadata-first
retrieval contract. They cover Nenjo Core reference material, Library knowledge,
filesystem-backed documentation, and package-installed knowledge.

Knowledge packs are for retrieval and explanation. They are not memory and they
are not task artifacts.

## Core Concepts

A knowledge pack provides:

- a stable tool selector such as `pkg:nenjo-ai.packages.knowledge.core` or a
  Library selector;
- document metadata with selector, title, summary, user-defined kind, tags, and source path;
- outbound graph edges between documents;
- lazy full-content reads after a document is selected.

Document selectors are stable agent-visible refs. Source paths are pack-local
file paths used by the resolver.

The `kind` field is user-defined pack metadata that categorizes the document
kind or purpose. Agents should use it as a routing hint, not as a closed
platform enum.

## Tools

Use these read tools for retrieval:

- `list_knowledge_packs`
- `search_knowledge`
- `list_knowledge_neighbors`
- `read_knowledge_doc`

Use these write tools only for Library knowledge when the active mode has the
proper Library write surface:

- `create_knowledge_pack`
- `update_knowledge_pack`
- `create_knowledge_doc`
- `update_knowledge_doc`
- `delete_knowledge_doc`

Package knowledge is installed from packages and should be treated as read-only
source material by agents. Agents should not mutate package knowledge packs.
User-managed Library packs are removed through the platform delete API, which
hard-deletes pack metadata, documents, edges, and object storage.

## Retrieval Flow

1. Use the rendered pack variable or `list_knowledge_packs` as an index.
2. Use `search_knowledge` to find candidate selectors.
3. Use `list_knowledge_neighbors` to follow outbound related edges.
4. Use `read_knowledge_doc` for selected full source documents.
5. Answer only from the documents actually read.

`list_knowledge_neighbors` returns outbound edges from the selected document. If
a concept should be reachable from a seed document, the seed must have an
outbound edge to it.

## Package Knowledge

Package-installed knowledge packs use the `pkg.<source_scope>.<source_repo>.<package_name>.<pack_name>`
prompt namespace. The Nenjo Core pack is exposed as:

```jinja
{{ pkg.nenjo_ai.packages.knowledge.core }}
```

The rendered value is an index. It is not a full documentation dump.

## Library Knowledge

Library knowledge is the platform-centered source material surface. Library
items sync into knowledge pack manifests so agents can search, traverse, and read
explicit source material without treating it as memory or project metadata.

## Pitfalls To Avoid

- Treating a pack index as full content.
- Answering from search summaries without reading the selected source docs.
- Treating package knowledge as an agent-mutable resource.
- Treating memory or artifacts as Library knowledge.
