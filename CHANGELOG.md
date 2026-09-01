# Changelog

This repository tracks public metadata for the **live** hosted ProductNow MCP
server. Entries are dated snapshots of when the catalog and docs were brought
in line with production — not software releases.

## 2026-09-01

- Refreshed `search_knowledge_warehouse` from backend MCP source: new
  `creatorNames`, `updatedAfter`, and `updatedBefore` filters, an optional
  `query` that switches the tool into a metadata listing mode ordered by latest
  content edit, and a rewritten tool description. Documented the filter
  semantics and result shape in [docs/TOOLS.md](docs/TOOLS.md) and the agent
  instructions.
- Corrected the `curate_knowledge_pack` display title to "Curate Context Pack"
  to match the annotation the server advertises.
- Corrected the catalog entry for `move_document_to_review`: it is the one tool
  that declares no output schema and returns text content without
  `structuredContent`.

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
