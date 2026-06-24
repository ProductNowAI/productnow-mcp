# ProductNow MCP Server

ProductNow's hosted Model Context Protocol (MCP) server lets AI clients create,
read, review, and update ProductNow documents, folders, templates, comments,
feedback, and status metadata through a secure OAuth-authenticated connection.

This repository is a public metadata and documentation package for marketplace
submissions. It does not contain the ProductNow application source code or the
MCP server implementation.

## Server

| Field | Value |
| --- | --- |
| Name | `ai.productnow/productnow` |
| Title | ProductNow |
| Version | `1.0.0` |
| Transport | Streamable HTTP |
| Endpoint | `https://api.productnow-prod.com/mcp` |
| Authentication | OAuth 2.0 via Auth0 |
| Required scope | `mcp:use` |
| Product app | `https://app.productnow.ai` |
| Marketing site | `https://productnow.ai` |

## What It Does

ProductNow turns prototype feedback into product decisions that teams can trace.
The MCP server exposes ProductNow workspace operations to compatible AI clients:

- Search, fetch, and create ProductNow documents.
- Read document versions, chat history, comment threads, feedback CSVs, and
  attached files.
- Ask ProductNow document and template agents to make edits.
- Create folders, templates, template sections, and template notes.
- Comment, reply, react, and update document status.

See [docs/TOOLS.md](docs/TOOLS.md) and [tools.json](tools.json) for the full
public tool catalog.

> **Note:** The tool catalog in this repository may be out of date. For the
> current list of tools, connect an MCP client to the server and use the
> standard `tools/list` request.

## Quick Start

Use any MCP client that supports remote Streamable HTTP servers and OAuth
discovery.

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

When the client first connects, ProductNow returns an OAuth challenge that points
the client to public discovery metadata. The client should complete the browser
login flow and then send the issued bearer token to the MCP endpoint.

See [docs/CLIENT_CONFIGURATION.md](docs/CLIENT_CONFIGURATION.md) for additional
configuration examples.

## Authentication And Discovery

ProductNow supports standard protected-resource discovery for MCP clients:

- MCP endpoint: `https://api.productnow-prod.com/mcp`
- Protected resource metadata:
  `https://api.productnow-prod.com/.well-known/oauth-protected-resource`
- MCP-specific protected resource metadata:
  `https://api.productnow-prod.com/.well-known/oauth-protected-resource/mcp`
- Authorization server metadata redirect:
  `https://api.productnow-prod.com/.well-known/oauth-authorization-server`

Unauthenticated requests receive a `401` response with a `WWW-Authenticate`
challenge containing the protected-resource metadata URL.

See [docs/AUTHENTICATION.md](docs/AUTHENTICATION.md) for details.

## Marketplace Metadata

This repository includes public files commonly used by MCP directories and
marketplaces:

- [server.json](server.json): official MCP Registry metadata.
- [mcp.json](mcp.json): general listing metadata for GitHub-based scrapers.
- [tools.json](tools.json): public tool catalog with safety annotations.
- [.well-known/glama.json](.well-known/glama.json): reference copy of Glama
  ownership metadata. The authoritative copy is served from the ProductNow API
  domain.
- [assets/productnow-icon.png](assets/productnow-icon.png): square icon.
- [assets/productnow-logo.png](assets/productnow-logo.png): full logo.

## Security And Permissions

All tool calls run as the authenticated ProductNow user. ProductNow applies its
normal organization, workspace, document, template, and comment permissions to
MCP requests. Clients should treat write tools as consequential actions and ask
for user confirmation when appropriate.

See [docs/PRIVACY_AND_PERMISSIONS.md](docs/PRIVACY_AND_PERMISSIONS.md).

## Support

For MCP listing or integration questions, contact `support@productnow.ai`.
