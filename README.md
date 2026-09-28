# ProductNow MCP Server

[![Status](https://status.productnow.ai/badge/v2?variant=outline)](https://status.productnow.ai/)

**Your team's company brain.**

ProductNow is the cross-vendor storage layer that joins your tools into one
shared context layer that AI can actually use. This hosted Model Context
Protocol (MCP) server makes that context layer available wherever you and your
team already work — through MCP clients that authenticate with OAuth and operate
as the signed-in ProductNow user.

This repository is a public metadata and documentation package for marketplace
submissions. It does not contain the ProductNow application source code or the
MCP server implementation.

[![MCP Registry](https://img.shields.io/badge/MCP_Registry-Listed-success)](https://registry.modelcontextprotocol.io/?q=ProductNow)
[![Smithery](https://img.shields.io/badge/Smithery-Listed-success)](https://smithery.ai/servers/productnow)
[![PulseMCP](https://img.shields.io/badge/PulseMCP-Listed-success)](https://www.pulsemcp.com/servers/productnow)
[![Glama](https://img.shields.io/badge/Glama-Listed-success)](https://glama.ai/mcp/connectors/ai.productnow/productnow)

## Server

| Field | Value |
| --- | --- |
| Name | `ai.productnow/productnow` |
| Title | ProductNow |
| Version | `1.1.0` |
| Transport | Streamable HTTP |
| Endpoint | `https://api.productnow-prod.com/mcp` |
| Authentication | OAuth 2.0 via Auth0 |
| Required scope | `mcp:use` |
| Product app | `https://app.productnow.ai` |
| Marketing site | `https://productnow.ai` |

## What It Does

ProductNow connects the tools where context already lives and turns that
signal into structured, queryable context — the living picture of your
organization. Through MCP, people and AI agents can search that company
brain, curate shareable knowledge packs, and collaborate on documents from
the clients they already use.

The MCP server exposes ProductNow workspace operations to compatible AI clients:

- `search` the company brain for grounded, citeable evidence excerpts —
  narrowed by folder, document creator, or last-edited date range, scoped to
  ProductNow product help, or switched to folder-name search.
- `fetch` a document (full text, sections, and comment threads) and
  `fetch_folder` to browse the warehouse root, a folder, or a folder tree.
- `remember` a fact, decision, or note and let ProductNow file it in the right
  document and folder in the background.
- Create documents with AI generation, `edit_document` through the document's
  editing agent, poll `get_status`, and `update_document_status` to snapshot,
  review, or publish.
- Organize with `create_folder`, `move`, `rename`, and `archive`.
- Collaborate with `comment_on_document` and `reply_to_thread`.
- Allocate media upload slots with `upload_media`.
- Curate shareable knowledge packs when the feature is enabled for the org.
- Create ProductNow help documents when the caller is a ProductNow org member.

See [docs/TOOLS.md](docs/TOOLS.md) and [tools.json](tools.json) for the full
public tool catalog (18 tools), and
[MCP_AGENT_INSTRUCTIONS.md](MCP_AGENT_INSTRUCTIONS.md) for the server's
initialization instructions and copy-paste agent guidance.

> **Note:** The tool catalog in this repository was refreshed from the backend
> MCP source on 2026-09-28. The live server's `tools/list` response remains the
> runtime source of truth for connected clients. Some tools are gated by
> organization membership or feature flags and may not appear for every user.

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
- [tools.json](tools.json): public tool catalog with safety annotations and
  output-schema availability.
- [.well-known/glama.json](.well-known/glama.json): reference copy of Glama
  ownership metadata. The authoritative copy is served from the ProductNow API
  domain.
- [assets/productnow-icon.png](assets/productnow-icon.png): square icon.
- [assets/productnow-logo.png](assets/productnow-logo.png): full logo.

## Security And Permissions

All tool calls run as the authenticated ProductNow user. ProductNow applies its
normal organization, workspace, document, and comment permissions to MCP
requests. Clients should treat write tools as consequential actions and ask for
user confirmation when appropriate.

See [docs/PRIVACY_AND_PERMISSIONS.md](docs/PRIVACY_AND_PERMISSIONS.md).

## Support

For MCP listing or integration questions, contact `support@productnow.ai`.
