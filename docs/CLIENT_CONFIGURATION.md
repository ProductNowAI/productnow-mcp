# Client Configuration

ProductNow works with MCP clients that support remote Streamable HTTP servers
and OAuth discovery.

## Generic MCP Client

```json
{
  "mcpServers": {
    "productnow": {
      "type": "streamable-http",
      "url": "https://api.productnow-prod.com/mcp"
    }
  }
}
```

Some clients use `transport` instead of `type`:

```json
{
  "servers": {
    "productnow": {
      "transport": "streamable-http",
      "url": "https://api.productnow-prod.com/mcp"
    }
  }
}
```

Use the field names expected by your MCP client.

## First Connection

On first connection, the client should receive an OAuth challenge from the MCP
endpoint, open a browser authorization flow, and then retry the request with a
bearer token.

If your client asks for server metadata, use:

```text
Name: productnow
Endpoint: https://api.productnow-prod.com/mcp
Transport: streamable-http
Authentication: OAuth 2.0
Scope: mcp:use
```

## Smoke Test

After authentication, call a read-only tool first:

```text
search_knowledge_warehouse
```

with a simple query such as `"what is this workspace about?"`, or call
`list_folders` to verify workspace access. That confirms the client can
authenticate and that ProductNow can resolve the user's organization.

## Write Tool Safety

Several tools can create or mutate ProductNow resources. Clients should ask the
user for confirmation before executing write or destructive tools, especially:

- `create_document`
- `create_help_document`
- `import_prototype`
- `move_document_to_review`
- `post_document_chat_message`
- `switch_document_chat_edit_mode`
- `upload_media`
