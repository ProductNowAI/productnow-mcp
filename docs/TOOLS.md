# Tool Catalog

The ProductNow MCP server exposes 18 tools. The machine-readable catalog is in
[../tools.json](../tools.json), refreshed from backend MCP source on
2026-09-28.

Every tool declares an input schema, an `outputSchema`, and returns
`structuredContent` alongside JSON text content for clients that support
structured tool results.

Some tools are gated at runtime:

- `create_help_document` is advertised only to members of ProductNow's
  organization.
- `curate_knowledge_pack` is advertised only when the
  `knowledge_pack_application` feature flag is enabled for the authenticated
  user. Clients display it under the title "Curate Knowledge Pack".

The server also sends static `instructions` in its MCP initialization response.
They are reproduced in [../MCP_AGENT_INSTRUCTIONS.md](../MCP_AGENT_INSTRUCTIONS.md).

## Recall

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `search` | Read | Yes | Search the company's shared context (or ProductNow help via `product_help` scope) for query-focused evidence excerpts with citations, or search folder names with `resultType: "folders"`. |
| `fetch` | Read | Yes | Read one document by id: full text, sections, comment threads, folder, and canonical URL. |
| `fetch_folder` | Read | Yes | List a folder or the warehouse root, optionally as a nested tree. |
| `list_document_versions` | Read | Yes | List every version of a document, newest first, optionally filtered by status. |
| `get_status` | Read | Yes | Report whether a document's draft agent is still `running` an edit or is `idle`. |

### `search`

| Input | Purpose |
| --- | --- |
| `query` | Topic or question to research. Optional for document search — see listing mode below. Required when `resultType` is `folders`. |
| `searchScope` | `workspace` (default) or `product_help` to search ProductNow help documentation exclusively. |
| `folderId` | Restrict the search to a folder and every folder nested under it. Resolve the id with `fetch_folder` (or a folder search) first. Ignored when `searchScope` is `product_help`. |
| `resultType` | `documents` (default) or `folders` to search folder names instead of documents. Folder search ignores `creatorNames` and date filters and is not available for `product_help`. |
| `creatorNames` | Restrict results to documents created by these workspace users. Each entry is a name or email substring and may match several users; use a full email for precision. `"me"` resolves to the calling user. Entries combine with OR. An empty list behaves the same as omitting the field; if no entry resolves to a user, the search returns no results. |
| `updatedAfter` | Lower bound (`YYYY-MM-DD`, UTC) on a document's latest active content edit. |
| `updatedBefore` | Upper bound (`YYYY-MM-DD`, UTC, inclusive of the whole day) on a document's latest active content edit. Must be on or after `updatedAfter`. |

Creator and date filters combine with AND; `creatorNames` entries combine with
OR. `creatorNames` matches document creators only — not editors or other
contributors.

**Search mode vs listing mode.** Pass `query` whenever the user names a topic,
even when filters are also present; results are relevance-filtered and each
source carries query-focused excerpts. Omit `query` (or pass an empty string)
only when the user asks purely which documents match creator or date filters —
the tool then lists documents by latest content edit, and those sources carry
metadata but no excerpts.

**Document result shape.** Every response returns the normalized `query`, a
`sources` array, and `failedSources`. Each source carries top-level citation
fields (`id`, `title`, `url`), a nested `document` block (`documentId`, `name`,
`createdAt`, plus `folderId` and `folderPath` when the document is filed in a
folder), an `evidenceSource` (`semantic_index`, `lexical_snippet`,
`document_fallback`, or `unavailable`), an optional `evidenceUpdatedAt`
freshness timestamp, and its `excerpts`. All result sets are capped by the
tool's source limit.

**Folder result shape.** When `resultType` is `folders`, the response carries a
`folders` array (best match first, capped) and empty `sources` /
`failedSources`. Each folder includes `folderId`, `name`, `updatedAt`,
`createdBy`, `isPrivate`, and — when nested — `parentFolderId` and `folderPath`.

### `fetch`

| Input | Purpose |
| --- | --- |
| `documentId` | The document to read. Use `search` to find document ids. |
| `documentVersionId` | Optional. Reads that version and its comments. Omit to read the latest draft. Use `list_document_versions` to find version ids. |

Returns `id` / `documentId`, `title` / `name`, `url`, `text` (every section
joined in order), `sections`, `threads` (each with its `threadId`, `status`,
`quotedText`, and `messages`), `documentVersionId`, `versionNumber`, `status`,
and `folderId` / `folderName` / `folderPath` when the document is filed in a
folder.

### `fetch_folder`

| Input | Purpose |
| --- | --- |
| `folderId` | Optional. The folder to list. Omit to list the warehouse root one level: top-level folders plus shared folders the user can see. |
| `deep` | Optional. With `folderId`, return that folder and every nested folder as a tree (up to 15 levels) with each folder's documents inside it. Rejected when listing the root. |

Each child folder and document includes its id, `name`, `updatedAt`,
`createdBy`, and `isPrivate`. Folders also include `contentCount`,
`subfolderCount`, and `parentFolderId` when nested. Documents also include
`status` (`DRAFT`, `REVIEW`, or `PUBLISHED`).

### `list_document_versions`

| Input | Purpose |
| --- | --- |
| `documentId` | The document whose versions to list. |
| `status` | Optional filter: `DRAFT`, `REVIEW`, `PUBLISHED`, `SNAPSHOT`, `OUTDATED`, or `ARCHIVED`. These are version statuses, not the action verbs accepted by `update_document_status`. |

### `get_status`

| Input | Purpose |
| --- | --- |
| `documentId` | The document whose draft edit to check. |

Returns `status` (`running` while any edit is queued or in progress; `idle` once
every edit sent so far has finished) and a short `note` of what the draft agent
last did. Poll it after `edit_document` or `create_document`, then `fetch` to
confirm the result.

## Remember

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `remember` | Destructive | Yes | Store one fact, decision, or note. ProductNow searches the warehouse, edits the document that covers the topic, or creates a new one in the right folder. Returns `success` immediately; the write runs in the background. |

### `remember`

| Input | Purpose |
| --- | --- |
| `content` | The information to remember, verbatim, with the context needed to understand it on its own (who, what, when). Cannot be blank. |
| `hint` | Optional placement hint: a document or folder name, or a topic the user named. Omit rather than passing a blank string. |

Do not search, fetch, or pick a folder before calling `remember`; do not poll
afterwards. Use `edit_document` or `create_document` only when the user named
the exact document or outlined a whole new document.

## Documents

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `create_document` | Write | Yes | Create a new document, file it in a folder, and generate its content with AI from `prompt` and `context`. |
| `edit_document` | Destructive | Yes | Hand editing instructions to the document's own editing agent. The only tool that changes document content. |
| `update_document_status` | Destructive | Yes | Promote the current draft with an action verb: `snapshot`, `review`, or `publish`. Each leaves a fresh draft. |

### `create_document`

| Input | Purpose |
| --- | --- |
| `name` | Document title (e.g. `RFC: API Key Generation`). |
| `prompt` | Generation instruction including a full outline: numbered sections, each with a title and what it should say. |
| `context` | Optional verbatim source material attached as a `.txt` file — notes, code, or the contents of a file the user named. |
| `folderId` | Folder to file the document in. Find it with `search` and `fetch_folder` first. Omit only when the user explicitly asked for the warehouse root. |
| `templateId` | Optional. Pass only when applying a specific template you already have. |

Returns `documentId` and `url`. Generation continues after the call returns;
poll `get_status` until `idle`.

### `edit_document`

| Input | Purpose |
| --- | --- |
| `documentId` | The document to edit. Resolve it with `search` or `fetch` first. |
| `message` | Instructions for the document's editing agent — name the section and the change, quote wording that must be preserved, and include all needed context. The agent cannot see the conversation. |

Returns before the edit finishes. Poll `get_status` until `idle`, then `fetch`
to confirm.

### `update_document_status`

| Input | Purpose |
| --- | --- |
| `documentId` | The document whose current draft should change status. |
| `status` | Action verb: `snapshot` (read-only copy), `review` (open for comments), or `publish` (make it the published version). Do not pass version statuses like `DRAFT` or `PUBLISHED`. |

Returns `documentId`, `documentVersionId`, `newDraftDocumentVersionId`,
`previousStatus`, and `status` as version statuses.

## Folders And Organization

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `create_folder` | Write | Yes | Create a folder only when no existing folder fits, nested under the closest related folder. |
| `move` | Destructive | Yes | Move documents and folders, several at once, into one folder or the root. |
| `rename` | Destructive | Yes | Change one document's or folder's name. |
| `archive` | Destructive | Yes | Archive documents and folders, several at once. Nothing is deleted. |

### `create_folder`

| Input | Purpose |
| --- | --- |
| `name` | Display name of the new folder. |
| `parentFolderId` | Optional. Folder to nest under. Omit only when the user explicitly asked for a root-level folder. |

### `move`

| Input | Purpose |
| --- | --- |
| `ids` | Document ids and folder ids to move, mixed together. |
| `parentFolderId` | Optional. Destination folder. Omit to move everything to the root. |

Returns `movedDocumentIds`, `movedFolderIds`, `permissionDeniedIds`,
`refusedIds` (folders that would be moved into themselves or a descendant), and
`unresolvedIds`. Everything else is still moved.

### `rename`

| Input | Purpose |
| --- | --- |
| `id` | The document id or folder id to rename. |
| `name` | The new name. |

Returns `id`, `kind` (`document` or `folder`), and `name`.

### `archive`

| Input | Purpose |
| --- | --- |
| `ids` | Document ids and folder ids to archive, mixed together. Archiving a folder also archives the contents the user can manage. |

Returns `archivedDocumentIds`, `archivedFolderIds`, `permissionDeniedIds`, and
`unresolvedIds`. Archived items leave listings and search; nothing is deleted.

## Comments

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `comment_on_document` | Write | Yes | Leave a comment on a document's current review version, posted as the user's personal agent. |
| `reply_to_thread` | Write | Yes | Reply to an existing comment thread as the user's personal agent. |

### `comment_on_document`

| Input | Purpose |
| --- | --- |
| `documentId` | The document to comment on. The server resolves its current review version and reports when there is none. |
| `comment` | The comment text. |
| `quotedText` | Optional document text or exact custom-node HTML to anchor the comment to. Use one target, not both. |

Returns `documentVersionId`, `threadId`, and `status`.

### `reply_to_thread`

| Input | Purpose |
| --- | --- |
| `threadId` | The thread to reply to. Thread ids come from `fetch`. |
| `message` | The reply message. |

## Knowledge Packs

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `curate_knowledge_pack` | Read (feature-flagged) | Yes | Curate a shareable knowledge pack URL from selected documents, notes, and highlights. |

## Help Documents

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `create_help_document` | Write (ProductNow org only) | Yes | Create a blank ProductNow help document for authorized ProductNow users. Optional `category`: `GETTING_STARTED`, `CONTENT_CREATION`, `COLLABORATION`, `ADMINISTRATION`, or `OTHER` (default). |

For product-help questions from any organization, use `search` with
`searchScope: "product_help"`.

## Media

| Tool | Access | Output Schema | Description |
| --- | --- | --- | --- |
| `upload_media` | Open-world write | Yes | Allocate a signed upload URL (valid 5 minutes) for ProductNow-hosted image or video media. Inputs: `fileName`, `mimeType` (`image/png`, `image/jpeg`, `image/gif`, `image/webp`, `video/mp4`, `video/webm`, `video/quicktime`). Returns `uploadUrl`, `url`, `htmlText`, and `storageObjectId`. |

## Safety Annotations

ProductNow exposes MCP tool annotations so clients can present safer UX:

- `readOnlyHint`: tool does not mutate ProductNow state.
- `destructiveHint`: tool may replace, delete, overwrite, or trigger edits.
- `idempotentHint`: repeated calls with the same arguments have no additional
  effect.
- `openWorldHint`: tool may interact with external URLs or upload flows outside
  ProductNow's closed workspace data.

Use [../tools.json](../tools.json) for the exact per-tool annotation values.
