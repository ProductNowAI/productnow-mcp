# Privacy And Permissions

ProductNow MCP requests run as the authenticated ProductNow user. The MCP server
does not grant broad workspace access on its own.

## Data The Server Can Access

Depending on the user's ProductNow permissions, tools may access:

- Document names, folder paths, versions, statuses, and section content.
- Document attachments requested through `get_attachment`.
- Document feedback exported as CSV.
- Document comment threads and thread messages.
- Template metadata, template sections, and `.templatepn` template content.
- Folder names and hierarchy visible to the user.

## Data The Server Can Modify

With sufficient user permissions, write tools can:

- Create documents, folders, templates, template sections, and template notes.
- Ask ProductNow document and template agents to edit content.
- Rename or replace templates and template versions.
- Delete templates or template sections.
- Add comments, replies, and reactions.
- Change document status tracking metadata.

## Client Guidance

MCP clients should:

- Show the authenticated ProductNow account when possible.
- Ask for confirmation before write or destructive tools.
- Avoid sending unrelated local files as `context` to `create_document`.
- Avoid storing returned attachment data unless the user explicitly requests it.
- Treat document and template content as private customer workspace data.

## ProductNow Guidance

The public metadata in this repository is safe to publish. Do not add:

- Application source code.
- Private API keys, OAuth client secrets, tokens, or cookies.
- Internal customer data, private document IDs, screenshots, or logs.
- Non-public staging URLs unless they are intentionally part of a listing.

