# Privacy And Permissions

ProductNow MCP requests run as the authenticated ProductNow user. The MCP server
does not grant broad workspace access on its own.

## Data The Server Can Access

Depending on the user's ProductNow permissions, tools may access:

- Knowledge warehouse search results: document metadata, query-focused excerpts,
  evidence source labels, and freshness timestamps.
- Document names, folder paths, versions, statuses, and section content.
- Document feedback exported as CSV.
- Document comment threads and thread messages.
- Help document metadata and content visible to the user (ProductNow help via
  `search_knowledge_warehouse` with `product_help` scope; help-document list/create
  tools are ProductNow-org-only).
- Folder names, hierarchy, and documents visible through folder listing/search.
- Knowledge pack curation inputs (document IDs, notes, highlight quotes) when the
  knowledge pack feature flag is enabled.
- Prototype import results, media storage object IDs, generated embed HTML, and
  signed upload URLs returned by media/prototype tools.

## Data The Server Can Modify

With sufficient user permissions, write tools can:

- Create documents and folders.
- Create blank ProductNow help documents for authorized ProductNow users.
- Ask ProductNow document agents to edit content via draft chat.
- Move draft document versions into review.
- Add comments and thread replies.
- Import prototype assets and allocate ProductNow media upload slots.

`curate_knowledge_pack` builds a shareable pack URL from documents the caller
can already view; it does not grant additional document access.

## Client Guidance

MCP clients should:

- Show the authenticated ProductNow account when possible.
- Ask for confirmation before write or destructive tools.
- Avoid sending unrelated local files as `context` to `create_document`.
- Use `import_prototype` only for user-requested URLs or inline prototype source
  content.
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
