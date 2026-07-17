# Privacy And Permissions

ProductNow MCP requests run as the authenticated ProductNow user. The MCP server
does not grant broad workspace access on its own.

## Data The Server Can Access

Depending on the user's ProductNow permissions, tools may access:

- Document names, folder paths, versions, statuses, and section content.
- Document attachments requested through `get_attachment`.
- Document feedback exported as CSV.
- Document comment threads and thread messages.
- Help document metadata and content visible to the user.
- Template metadata, template sections, and `.templatepn` template content.
- Folder names and hierarchy visible to the user.
- Prototype import results, media storage object IDs, generated embed HTML, and
  signed upload URLs returned by media/prototype tools.

## Data The Server Can Modify

With sufficient user permissions, write tools can:

- Create documents, folders, templates, template sections, and template notes.
- Create blank ProductNow help documents for authorized ProductNow users.
- Ask ProductNow document and template agents to edit content.
- Rename or replace templates and template versions.
- Delete templates or template sections.
- Add comments, replies, and reactions.
- Change document status tracking metadata.
- Import prototype assets and allocate ProductNow media upload slots.

## Client Guidance

MCP clients should:

- Show the authenticated ProductNow account when possible.
- Ask for confirmation before write or destructive tools.
- Avoid sending unrelated local files as `context` to `create_document`.
- Avoid storing returned attachment data unless the user explicitly requests it.
- Use `import_prototype` only for user-requested URLs or inline prototype source
  content.
- Treat `upload_media` upload URLs as short-lived write credentials and do not
  share them beyond the current user workflow.
- Treat document and template content as private customer workspace data.

## ProductNow Guidance

The public metadata in this repository is safe to publish. Do not add:

- Application source code.
- Private API keys, OAuth client secrets, tokens, or cookies.
- Internal customer data, private document IDs, screenshots, or logs.
- Non-public staging URLs unless they are intentionally part of a listing.
