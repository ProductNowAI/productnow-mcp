# Changelog

This repository tracks public metadata for the **live** hosted ProductNow MCP
server. Entries are dated snapshots of when the catalog and docs were brought
in line with production — not software releases.

## 2026-09-28

- Refreshed the public tool catalog from backend MCP source: 18 tools. The
  surface was consolidated around a recall / remember / write model:
  - Renamed `search_knowledge_warehouse` → `search`. It now also searches
    folder names via `resultType: "folders"` (replacing `search_folders`) and
    scopes by `folderId` (previously `destinationFolderId`). Every source now
    carries top-level `id`, `title`, and `url` citation fields plus `folderId`
    / `folderPath`.
  - Renamed `get_document` → `fetch`, which now returns the document's full
    `text`, `url`, folder, and its comment threads and messages inline
    (replacing `get_document_threads` and `get_thread_messages`).
  - Replaced `list_folders` with `fetch_folder`, which lists the root, one
    folder, or a nested tree (`deep: true`).
  - Added `remember`: a fire-and-forget tool that stores a fact and lets
    ProductNow choose the document and folder in the background.
  - Replaced `post_document_chat_message`, `switch_document_chat_edit_mode`,
    and `get_document_chat` with `edit_document` (instructions for the
    document's editing agent, applied automatically) and `get_status`
    (`running` / `idle` polling).
  - Replaced `move_document_to_review` with `update_document_status`, which
    takes the action verbs `snapshot`, `review`, or `publish`.
  - Renamed `create_document_comment` → `comment_on_document`; it now targets a
    `documentId` and resolves the current review version itself.
  - Added `move`, `rename`, and `archive` for organizing documents and folders
    (mixed ids, several at once for `move` and `archive`).
  - Removed `import_prototype`, `get_document_feedback`, and
    `list_help_documents` from the public catalog.
  - Rewrote descriptions for `create_document` (folder placement guidance,
    `templateId` instead of `templateVersionId`), `create_folder`,
    `list_document_versions`, and `reply_to_thread`.
- Reproduced the server's MCP initialization `instructions` in
  [MCP_AGENT_INSTRUCTIONS.md](MCP_AGENT_INSTRUCTIONS.md) and rewrote the
  workflows, tool reference, anti-patterns, and copy-paste system prompt around
  the new tools.
- Documented per-tool inputs and result shapes in [docs/TOOLS.md](docs/TOOLS.md)
  and updated client configuration and privacy docs to the new tool names.
- Bumped public metadata to `1.1.0` and updated listing copy from "knowledge
  warehouse" to "company brain" / "shared context layer" (`server.json`,
  `mcp.json`, `tools.json`, and the docs).

## 2026-09-01

- Refreshed `search_knowledge_warehouse` from backend MCP source: new
  `creatorNames`, `updatedAfter`, and `updatedBefore` filters, an optional
  `query` that switches the tool into a metadata listing mode ordered by latest
  content edit, and a rewritten tool description. Documented the filter
  semantics and result shape in [docs/TOOLS.md](docs/TOOLS.md) and the agent
  instructions.

## 2026-08-20

- Refreshed the public tool catalog from backend MCP source: 21 tools aligned to
  the current hosted server (`search_knowledge_warehouse`, `search_folders`,
  `move_document_to_review`, `curate_knowledge_pack`, and related document /
  collaboration / media tools). Removed retired template, attachment, reaction,
  and document-status tools from the public catalog.
- Documented runtime tool visibility for ProductNow-org-only help tools and the
  feature-flagged knowledge pack tool.
- Updated marketing copy to ProductNow's current positioning: knowledge
  warehouse / living knowledge layer available through MCP (preferring
  "knowledge" over "context" in user-facing copy).

## 2026-06-25

- Initial public metadata package for the hosted ProductNow MCP server.
- Includes official registry metadata, general marketplace metadata, tool
  catalog, setup docs, authentication docs, and public logo assets.
