# Tool Catalog

> **Note:** This catalog may be out of date. For the current list of tools,
> connect an MCP client to `https://api.productnow-prod.com/mcp` and use the
> standard `tools/list` request.

The ProductNow MCP server exposes 33 tools. The machine-readable catalog is in
[../tools.json](../tools.json).

## Documents

| Tool | Access | Description |
| --- | --- | --- |
| `search_documents` | Read | Search ProductNow documents by name. |
| `get_document` | Read | Retrieve document content, version metadata, status, folder path, and sections. |
| `create_document` | Write | Create a ProductNow document and start AI generation. |
| `list_document_versions` | Read | List versions for a document, optionally filtered by status. |
| `get_document_chat` | Read | Fetch document draft chat history and edit mode. |
| `post_document_chat_message` | Write | Send a message to the document draft agent. |
| `switch_document_chat_edit_mode` | Write | Switch a document agent between ask-before-edit and automatic edit modes. |
| `get_document_version_status` | Read | Read status tracking for a document version. |
| `change_document_version_status` | Write | Update status tracking for a document version. |
| `get_attachment` | Read | Retrieve a document attachment as a base64-encoded file. |

## Folders

| Tool | Access | Description |
| --- | --- | --- |
| `list_folders` | Read | List folders visible to the authenticated user. |
| `create_folder` | Write | Create a folder, optionally nested under a parent folder. |

## Templates

| Tool | Access | Description |
| --- | --- | --- |
| `search_templates` | Read | Search templates by name and return template version IDs. |
| `list_templates` | Read | List templates available to the user, with optional filters. |
| `create_template` | Write | Create a new ProductNow template. |
| `edit_template` | Write | Rename a template. |
| `delete_template` | Destructive | Permanently delete a template. |
| `get_template_content` | Read | Retrieve latest template content as `.templatepn` JSON. |
| `edit_template_version` | Destructive | Replace a template version with new `.templatepn` JSON. |
| `edit_template_version_metadata` | Write | Update template metadata fields. |
| `get_template_chat` | Read | Fetch template agent chat history and edit mode. |
| `post_template_chat_message` | Write | Send a message to the template agent. |
| `switch_template_edit_mode` | Write | Switch a template agent between ask-before-edit and automatic edit modes. |
| `create_template_section` | Write | Add a section to a template version. |
| `edit_template_section` | Write | Edit fields on a template section. |
| `delete_template_section` | Destructive | Delete a template section. |
| `create_template_note` | Write | Add a private note to a template version. |

## Comments And Feedback

| Tool | Access | Description |
| --- | --- | --- |
| `create_document_comment` | Write | Comment on a review-status document version. |
| `get_document_threads` | Read | List comment threads on a document version. |
| `get_thread_messages` | Read | Fetch messages in a comment thread. |
| `reply_to_thread` | Write | Reply to a comment thread as the user's personal agent. |
| `react_to_message` | Write | Add an emoji reaction to a thread message. |
| `get_document_feedback` | Read | Retrieve document feedback as CSV. |

## Safety Annotations

ProductNow exposes MCP tool annotations so clients can present safer UX:

- `readOnlyHint`: tool does not mutate ProductNow state.
- `destructiveHint`: tool may replace, delete, overwrite, or trigger edits.
- `idempotentHint`: repeated calls have no additional effect.
- `openWorldHint`: tool interacts with persisted ProductNow state.

Use [../tools.json](../tools.json) for the exact per-tool annotation values.

