# Authentication

ProductNow's MCP server is a hosted remote server. It uses OAuth 2.0 through
Auth0 and requires bearer tokens scoped for MCP access.

## Endpoint

```text
https://api.productnow-prod.com/mcp
```

## Discovery

MCP clients should discover authorization details through ProductNow's public
protected-resource metadata endpoints:

```text
https://api.productnow-prod.com/.well-known/oauth-protected-resource
https://api.productnow-prod.com/.well-known/oauth-protected-resource/mcp
```

The authorization server metadata redirect is available at:

```text
https://api.productnow-prod.com/.well-known/oauth-authorization-server
```

## Challenge Flow

Unauthenticated MCP requests receive `401 Unauthorized` with a
`WWW-Authenticate` header similar to:

```text
WWW-Authenticate: Bearer resource_metadata="https://api.productnow-prod.com/.well-known/oauth-protected-resource"
```

The MCP client should:

1. Fetch the protected-resource metadata URL from the challenge.
2. Discover the authorization server.
3. Complete the browser OAuth login flow.
4. Request the `mcp:use` scope.
5. Send future MCP requests with an `Authorization: Bearer <token>` header.

## Authorization Model

ProductNow evaluates MCP requests as the authenticated user. A token only
establishes identity; every tool call still goes through ProductNow's normal
workspace and resource authorization checks.

For example:

- A user can only read documents they can view in ProductNow.
- Template writes require the user's template permissions.
- Comment replies and reactions require comment access to the document.
- Folder and document creation happens inside the user's organization context.

## Token Handling

Do not commit tokens or static credentials to this repository. Clients should
store OAuth tokens through their own secure credential storage.

