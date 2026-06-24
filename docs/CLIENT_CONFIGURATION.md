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
list_folders
```

That verifies the client can authenticate and that ProductNow can resolve the
user's workspace context.

## Write Tool Safety

Several tools can create, edit, or delete ProductNow resources. Clients should
ask the user for confirmation before executing write or destructive tools,
especially:

- `delete_template`
- `delete_template_section`
- `edit_template_version`
- `post_document_chat_message`
- `post_template_chat_message`
- `switch_document_chat_edit_mode`
- `switch_template_edit_mode`

