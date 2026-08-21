# Tool Catalog

The ProductNow MCP server exposes 21 tools. The machine-readable catalog is in
[../tools.json](../tools.json), refreshed from backend MCP source on
2026-08-20.

Every tool declares an input schema and an `outputSchema`, and every handler
returns `structuredContent` alongside JSON text content for clients that support
structured tool results.

Some tools are gated at runtime:

- `create_help_document` and `list_help_documents` are advertised only to
  members of ProductNow's organization.
- `curate_knowledge_pack` is advertised only when the
  `knowledge_pack_application` feature flag is enabled for the authenticated
  user.

## Knowledge Warehouse

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `search_knowledge_warehouse` | Read | Yes | Search workspace knowledge (or ProductNow help via `product_help` scope) for query-focused evidence excerpts. |
| `curate_knowledge_pack` | Read (feature-flagged) | Yes | Curate a shareable knowledge pack URL from selected documents, notes, and highlights. |

## Documents

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `get_document` | Read | Yes | Retrieve document content, version metadata, folder path, and sections. |
| `create_document` | Write | Yes | Create a ProductNow document and start AI generation. |
| `list_document_versions` | Read | Yes | List versions for a document, optionally filtered by status. |
| `move_document_to_review` | Destructive | Yes | Move a draft version into review and create a fresh draft for further edits. |
| `get_document_chat` | Read | Yes | Fetch document draft chat history and edit mode. |
| `post_document_chat_message` | Write | Yes | Send a message to the document draft agent. |
| `switch_document_chat_edit_mode` | Destructive | Yes | Switch a document agent between ask-before-edit and automatic edit modes. |

## Folders

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `search_folders` | Read | Yes | Fuzzy-search folders by name to resolve a `folderId`. |
| `list_folders` | Read | Yes | List folders and documents visible to the authenticated user. |
| `create_folder` | Write | Yes | Create a folder, optionally nested under a parent folder. |

## Help Documents

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `list_help_documents` | Read (ProductNow org only) | Yes | List help documents visible to the authenticated ProductNow user, optionally by category. |
| `create_help_document` | Write (ProductNow org only) | Yes | Create a blank ProductNow help document for authorized ProductNow users. |

For product-help questions from any organization, prefer
`search_knowledge_warehouse` with `searchScope: "product_help"`.

## Comments And Feedback

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `create_document_comment` | Write | Yes | Comment on a review-status document version. |
| `get_document_threads` | Read | Yes | List comment threads on a document version. |
| `get_thread_messages` | Read | Yes | Fetch messages in a comment thread. |
| `reply_to_thread` | Write | Yes | Reply to a comment thread as the user's personal agent. |
| `get_document_feedback` | Read | Yes | Retrieve document feedback as CSV. |

## Media And Prototypes

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `import_prototype` | Open-world write | Yes | Import a prototype from an allowlisted URL or inline HTML, JSX, or TSX. |
| `upload_media` | Open-world write | Yes | Allocate a signed upload URL for ProductNow-hosted image or video media. |

## Safety Annotations

ProductNow exposes MCP tool annotations so clients can present safer UX:

- `readOnlyHint`: tool does not mutate ProductNow state.
- `destructiveHint`: tool may replace, delete, overwrite, or trigger edits.
- `idempotentHint`: repeated calls have no additional effect.
- `openWorldHint`: tool may interact with external URLs or upload flows outside
  ProductNow's closed workspace data.

Use [../tools.json](../tools.json) for the exact per-tool annotation values.
