# Artifacts — Immutable Organization Files

## Purpose

Artifacts are organization-owned, immutable byte snapshots. They provide a
durable identity for generated deliverables, uploaded files, task inputs, and
routine handoffs without treating workspace paths or mutable files as durable
references.

The dashboard presents artifacts as a filesystem-like catalog under the
Library's Artifacts tab. That placement is navigation, not a type conversion:
artifacts remain distinct from indexed Library knowledge and from agent memory.

## Identity, Paths, And Revisions

- An artifact ID identifies exactly one immutable revision.
- A lineage groups revisions of the same logical file.
- A catalog path such as `Clients/Acme/report.pdf` points to the current
  revision in that lineage.
- Reading by artifact ID reproduces a historical revision; reading by catalog
  path resolves the current revision.
- Publishing a revision requires the current artifact ID. Stale revision IDs
  are rejected instead of silently overwriting concurrent work.

## Runtime Access

- typed immutable artifact inputs on chat or task requests;
- `list_artifacts` and `read_artifact` when platform scope permits;
- workspace tools for files in the current execution scope.

Artifacts are not prompt template variables.

Artifact catalog paths are organization-relative. They are not worker
workspace paths, object-store keys, or authorization tokens.

## Agent Tools

Use `list_artifacts` to browse the catalog one directory at a time. Pass an
empty path for the root. Results include immediate child folders, current
artifact revisions, and any unfiled artifacts; they do not expose storage keys
or signed URLs.

Use `read_artifact` with either selector:

```json
{"selector":{"kind":"path","path":"Designs/agent.md"}}
```

```json
{"selector":{"kind":"artifact_id","artifact_id":"<uuid>"}}
```

The default `automatic` view returns bounded lines for textual artifacts. For
images, documents, audio, and other supported media, it emits a typed immutable
artifact input for model inspection. Use `view: "text"`, `start_line`, and
`line_count` when a textual artifact needs an explicit bounded range.

Use `upload_artifact` to publish a regular file already present inside the
worker workspace. New logical artifacts require a catalog path:

```json
{
  "source_path": "outputs/report.pdf",
  "target": {"kind":"create","publish_path":"Reports/report.pdf"}
}
```

A revision requires the current immutable artifact ID and retains its catalog
placement:

```json
{
  "source_path": "outputs/report-v2.pdf",
  "target": {"kind":"revision","revision_of":"<current-artifact-uuid>"}
}
```

`media_type` is optional when the file extension is sufficient. Uploads are
encrypted before leaving the trusted worker host and are limited to 16 MiB.
The successful result includes the immutable artifact ID, lineage, revision,
media type, size, digest, and catalog path when applicable.

## Attachments And Model Inspection

Dashboard chat and task attachment flows upload the selected file behind the
scenes and send its immutable artifact reference with the request. Users should
not have to publish a file manually and then select it again.

The worker verifies and decrypts artifact bytes inside its trusted boundary.
Verified plaintext may be cached there because immutable ID, size, and digest
make reuse safe. If the primary chat model accepts the media directly, the
runtime attaches it to that model request. Otherwise it uses a configured
organization model capability such as `analyze_image`, `analyze_document`, or
`transcribe_audio` and records the resulting analysis in the conversation.
Unsupported media/provider combinations fail explicitly; agents must not claim
they inspected content that the runtime could not route.

## Routine Handoffs And Human Review

When a handoff contains an artifact ID, declare the property semantically:

```yaml
report_artifact:
  type: string
  format: nenjo-artifact-id
```

Only schema properties with `format: nenjo-artifact-id` are extracted,
tenant-authorized, and rendered as review artifacts. A UUID-looking ordinary
string is not an artifact reference.

## Permissions And Boundaries

The agent tool policy exposes `list_artifacts` and `read_artifact` with
`artifacts:read`, and additionally exposes `upload_artifact` with
`artifacts:write`. Write implies read only within the artifact resource family;
task scopes grant no artifact access. Scope assignment remains a user/platform
action. Agents and abilities must not grant these scopes to themselves.

Agent tools currently do not rename, move, delete, or rewrite finalized
artifacts. Do not model artifacts as memory facts, inject artifact indexes
through invented template variables, or imply that uploading an artifact also
creates searchable Library knowledge.
