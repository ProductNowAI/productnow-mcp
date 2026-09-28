# Privacy And Permissions

ProductNow MCP requests run as the authenticated ProductNow user. The MCP server
does not grant broad workspace access on its own.

## Data The Server Can Access

Depending on the user's ProductNow permissions, tools may access:

- Company brain search results: document metadata, canonical document
  URLs, query-focused excerpts, evidence source labels, freshness timestamps,
  and matching folder names.
- Document names, folder locations, versions, statuses, section content, and
  full document text.
- Document comment threads and thread messages (returned inline by `fetch`).
- Draft-agent edit status (`running` / `idle`) and a short note of the agent's
  last action, via `get_status`.
- Help document content visible to the user (ProductNow help via `search` with
  `product_help` scope; the help-document create tool is ProductNow-org-only).
- Folder names, hierarchy, creators, privacy flags, and documents visible
  through `fetch_folder` and folder search.
- Knowledge pack curation inputs (document IDs, notes, highlight quotes) when the
  knowledge pack feature flag is enabled.
- Media storage object IDs, generated embed HTML, and signed upload URLs
  returned by `upload_media`.

## Data The Server Can Modify

With sufficient user permissions, write tools can:

- Create documents and folders.
- Create blank ProductNow help documents for authorized ProductNow users.
- Store a remembered fact by editing an existing document or creating a new one,
  chosen by ProductNow in the background (`remember`).
- Ask a document's editing agent to change its content (`edit_document`). Edits
  are applied automatically without a second approval in the ProductNow UI.
- Snapshot, move into review, or publish the current draft
  (`update_document_status`).
- Move, rename, and archive documents and folders. Archiving hides items from
  listings and search; nothing is deleted.
- Add comments and thread replies.
- Allocate ProductNow media upload slots.

`curate_knowledge_pack` builds a shareable pack URL from documents the caller
can already view; it does not grant additional document access.

## Client Guidance

MCP clients should:

- Show the authenticated ProductNow account when possible.
- Ask for confirmation before write or destructive tools.
- Avoid sending unrelated local files as `context` to `create_document`, and
  avoid sending conversation content the user did not ask to save to
  `remember`.
- Treat `upload_media` upload URLs as short-lived write credentials and do not
  share them beyond the current user workflow.
- Treat document content and warehouse excerpts as private customer workspace
  data.

## ProductNow Guidance

The public metadata in this repository is safe to publish. Do not add:

- Application source code.
- Private API keys, OAuth client secrets, tokens, or cookies.
- Internal customer data, private document IDs, screenshots, or logs.
- Non-public staging URLs unless they are intentionally part of a listing.
