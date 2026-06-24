# Marketplace Submission

This repository is structured so MCP marketplaces and directories can scrape a
public GitHub source of truth without requiring ProductNow application source
code.

## Primary Listing Facts

| Field | Value |
| --- | --- |
| Name | `ai.productnow/productnow` |
| Title | ProductNow |
| Description | Turn prototype feedback into product decisions that your whole team can trace. |
| Endpoint | `https://api.productnow-prod.com/mcp` |
| Transport | `streamable-http` |
| Authentication | OAuth 2.0 via Auth0 |
| Scope | `mcp:use` |
| Website | `https://app.productnow.ai` |
| Maintainer email | `support@productnow.ai` |

## Included Files

| File | Purpose |
| --- | --- |
| `README.md` | Human-readable listing page. |
| `server.json` | Official MCP Registry metadata. |
| `mcp.json` | General public metadata for GitHub-based scrapers. |
| `tools.json` | Public catalog of tools and safety annotations. |
| `.well-known/glama.json` | Reference copy of Glama ownership metadata. |
| `assets/productnow-icon.png` | Square marketplace icon. |
| `assets/productnow-logo.png` | Full ProductNow logo. |
| `docs/AUTHENTICATION.md` | OAuth and discovery details. |
| `docs/CLIENT_CONFIGURATION.md` | Example client setup. |
| `docs/PRIVACY_AND_PERMISSIONS.md` | Permission and data-handling guidance. |

## Official MCP Registry

Use `server.json` as the registry source:

```bash
mcp-publisher validate server.json
mcp-publisher publish server.json
```

ProductNow publishes under:

```text
ai.productnow/productnow
```

Ownership is verified through DNS for `productnow.ai`.

## Glama

Glama verifies ownership by fetching:

```text
https://api.productnow-prod.com/.well-known/glama.json
```

This repository includes `.well-known/glama.json` as a reference copy, but the
API-domain hosted file is the authoritative verification document.

## Suggested GitHub Repository Settings

- Visibility: public.
- Repository description: `ProductNow MCP server metadata and setup docs`.
- Website: `https://app.productnow.ai`.
- Topics: `mcp`, `model-context-protocol`, `product-management`,
  `prototyping`, `feedback`, `documents`, `oauth`.

## Pre-Submission Checklist

- Confirm `server.json` endpoint and version match production.
- Confirm `mcp.json` endpoint, URLs, maintainer email, and assets are current.
- Confirm `tools.json` matches the live `tools/list` response.
- Confirm `https://api.productnow-prod.com/.well-known/oauth-protected-resource`
  returns the expected protected-resource metadata.
- Confirm `https://api.productnow-prod.com/.well-known/glama.json` returns the
  expected maintainer email.
- Confirm logo files render correctly in GitHub.
