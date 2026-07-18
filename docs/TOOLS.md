# Tool Catalog

The ProductNow MCP server exposes 38 tools. The machine-readable catalog is in
[../tools.json](../tools.json), refreshed from backend MCP source on
2026-07-17.

Every tool declares an input schema and an `outputSchema`, and every handler
returns `structuredContent` alongside JSON text content for clients that support
structured tool results.

## Documents

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `search_documents` | Read | Yes | Search ProductNow documents by name. |
| `get_document` | Read | Yes | Retrieve document content, version metadata, status, folder path, and sections. |
| `create_document` | Write | Yes | Create a ProductNow document and start AI generation. |
| `list_document_versions` | Read | Yes | List versions for a document, optionally filtered by status. |
| `get_document_chat` | Read | Yes | Fetch document draft chat history and edit mode. |
| `post_document_chat_message` | Write | Yes | Send a message to the document draft agent. |
| `switch_document_chat_edit_mode` | Destructive | Yes | Switch a document agent between ask-before-edit and automatic edit modes. |
| `get_document_version_status` | Read | Yes | Read status tracking for a document version. |
| `change_document_version_status` | Destructive | Yes | Update status tracking for a document version. |
| `get_attachment` | Read | Yes | Retrieve a document attachment as a base64-encoded file. |

## Folders

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `list_folders` | Read | Yes | List folders visible to the authenticated user. |
| `create_folder` | Write | Yes | Create a folder, optionally nested under a parent folder. |

## Templates

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `search_templates` | Read | Yes | Search templates by name and return template version IDs. |
| `list_templates` | Read | Yes | List templates available to the user, with optional filters. |
| `create_template` | Write | Yes | Create a new ProductNow template. |
| `edit_template` | Destructive | Yes | Rename a template. |
| `delete_template` | Destructive | Yes | Permanently delete a template. |
| `get_template_content` | Read | Yes | Retrieve latest template content as `.templatepn` JSON. |
| `edit_template_version` | Destructive | Yes | Replace a template version with new `.templatepn` JSON. |
| `edit_template_version_metadata` | Destructive | Yes | Update template metadata fields. |
| `get_template_chat` | Read | Yes | Fetch template agent chat history and edit mode. |
| `post_template_chat_message` | Write | Yes | Send a message to the template agent. |
| `switch_template_edit_mode` | Destructive | Yes | Switch a template agent between ask-before-edit and automatic edit modes. |
| `create_template_section` | Write | Yes | Add a section to a template version. |
| `edit_template_section` | Destructive | Yes | Edit fields on a template section. |
| `delete_template_section` | Destructive | Yes | Delete a template section. |
| `create_template_note` | Write | Yes | Add a private note to a template version. |

## Help Documents

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `search_help_documents` | Read | Yes | Search ProductNow help documents by name. |
| `list_help_documents` | Read | Yes | List help documents visible to the authenticated user, optionally by category. |
| `create_help_document` | Write | Yes | Create a blank ProductNow help document for authorized ProductNow users. |

## Comments And Feedback

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `create_document_comment` | Write | Yes | Comment on a review-status document version. |
| `get_document_threads` | Read | Yes | List comment threads on a document version. |
| `get_thread_messages` | Read | Yes | Fetch messages in a comment thread. |
| `reply_to_thread` | Write | Yes | Reply to a comment thread as the user's personal agent. |
| `react_to_message` | Write | Yes | Add an emoji reaction to a thread message. |
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
