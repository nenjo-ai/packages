# Resource Surface Taxonomy

## Purpose

Resource surfaces classify where a Nenjo concept should be handled: platform UI,
platform tools, SDK manifests, runtime code, Library knowledge, packages, or
shared semantics.

## Surface Classes

| Surface | Meaning | Indicators |
|---|---|---|
| `platform_ui` | Dashboard/editor fields shown to users | Chat, agent builder, routine editor, Library, projects, settings, workers |
| `platform_tools` | Platform-backed tools and API operations | User asks Nenji to inspect, create, update, or verify live resources |
| `sdk_manifest` | Local declarative manifest files | User asks for manifests, package files, import/export, or SDK config |
| `sdk_runtime` | Runtime structs, builders, crates, traits, and execution APIs | User asks about embedding Nenjo, provider setup, tools, memory, or workers |
| `sdk_runtime_boundary` | Crate ownership and worker/harness separation | User asks where code lives or what owns sessions/tools/events |
| `library_knowledge` | Platform source material exposed through knowledge tools | User asks about policies, docs, source material, Library items, or graph retrieval |
| `package_resource` | Installable bundle and package-installed resources | User asks about `packages.yaml`, `nenjo.package.v1`, `pkg.*`, or package modules |
| `permission_ref` | Platform scope and tool availability | User asks why a tool is unavailable or how permissions are granted |
| `shared_fields` | Semantics common across surfaces | User asks what a field means independent of UI/API/manifest |

## Examples

| User Request | Surface | Guidance |
|---|---|---|
| "I need an agent that reviews incidents." | `platform_tools`, `shared_fields` | Design the agent and recommend platform reads/writes |
| "Give me the manifest for that agent." | `sdk_manifest` | Provide local manifest structure |
| "How should my agent read project reference material?" | `library_knowledge` | Route to Library knowledge tools |
| "How does the packages repo expose Nenji?" | `package_resource` | Explain registry, package manifest, modules, imports, and `pkg.*` selectors |
| "Why can’t Nenji update this resource?" | `permission_ref` | Explain missing scope family and user-controlled scope assignment |

## Agent Guidance

Classify surface before answering. Do not give platform write instructions for
package manifests unless the user is explicitly editing local SDK/package files.
