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
search
```

with a simple query such as `"what is this workspace about?"`, or call
`fetch_folder` with no arguments to list the warehouse root. That confirms the
client can authenticate and that ProductNow can resolve the user's
organization.

## Write Tool Safety

Several tools can create or mutate ProductNow resources. Clients should ask the
user for confirmation before executing write or destructive tools, especially:

- `archive`
- `create_document`
- `create_help_document`
- `edit_document`
- `move`
- `remember`
- `rename`
- `update_document_status`
- `upload_media`

`remember`, `create_document`, and `edit_document` return before their writes
finish. `remember` runs entirely in the background and should not be polled;
for the other two, agents should poll `get_status` until `idle` and then
`fetch` to verify the result.
