# Weio site check: a remote MCP server for website facts

<!-- mcp-name: ai.weio/site-check -->

A hosted [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI agents three read-only tools:

| Tool | What it returns |
|---|---|
| `check_https(domain)` | For `example.com` and `www.example.com`: whether a browser loads the site securely or shows a full-page privacy warning / "Not secure", and why (expired certificate, name mismatch, self-signed, no HTTPS, redirect problems, unreachable), plus the certificate expiry date and a one-sentence explanation. |
| `site_info(domain)` | What a business publishes on its homepage: title and description, language, CMS or site builder (WordPress, Wix, Squarespace, Shopify...), mobile viewport tag, role contact emails (info@, sales@...; personal-name addresses are left out on purpose), phone numbers, social links and the contact/about page. |
| `find_businesses(category, area)` | Up to 10 records from a small, dated local scan index (currently dental businesses in Fresno County, California, scanned 2026-09-30). Name, website and a phone-layout flag; no emails or personal data. |

- **Endpoint:** `https://weio.ai/mcp` (streamable HTTP, stateless)
- **Free:** 10 calls a day, no key, no signup
- **Paid:** an API key with 1,000 calls for $9 from [weio.ai/services/site-check-api.html](https://weio.ai/services/site-check-api.html?utm_source=github&utm_medium=repo&utm_campaign=site-check-mcp). Send it as `Authorization: Bearer wk_...` or `X-API-Key: wk_...`.
- **Registry:** listed in the official MCP registry as [`ai.weio/site-check`](https://registry.modelcontextprotocol.io/v0/servers?search=ai.weio)

Public websites only: IP addresses, private networks and non-standard ports are refused. `site_info` reads one homepage and does not crawl.

## What this repository is

The server runs on Weio's infrastructure and its source code is not published. This repository holds the public interface: the tool schemas ([`tools.json`](tools.json)), the registry entry ([`server.json`](server.json)), client configuration files and examples. Issues and questions are welcome here.

## Connect

**Claude Code**

```bash
claude mcp add --transport http weio-site-check https://weio.ai/mcp
# with a paid key:
claude mcp add --transport http weio-site-check https://weio.ai/mcp --header "Authorization: Bearer wk_..."
```

**Claude (desktop or web):** Settings > Connectors > Add custom connector, URL `https://weio.ai/mcp`.

**Cursor** (`.cursor/mcp.json`) and other clients that read `mcpServers`:

```json
{ "mcpServers": { "weio-site-check": { "url": "https://weio.ai/mcp" } } }
```

**VS Code** (`.vscode/mcp.json`):

```json
{ "servers": { "weio-site-check": { "type": "http", "url": "https://weio.ai/mcp" } } }
```

**Gemini CLI:** `gemini extensions install https://github.com/weioai/site-check-mcp`, or add to `settings.json`:

```json
{ "mcpServers": { "weio-site-check": { "httpUrl": "https://weio.ai/mcp" } } }
```

**Smithery:** [smithery.ai/servers/weio/site-check](https://smithery.ai/servers/weio/site-check) (set the optional `apiKey` setting to use a paid key).

To pass a key in any client that supports headers, add `"headers": { "Authorization": "Bearer wk_..." }`.

## Try it without a client

```bash
curl -s https://weio.ai/mcp \
  -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"check_https","arguments":{"domain":"example.com"}}}'
```

More in [`examples/`](examples). The same checks are also available as plain HTTP: `GET https://weio.ai/api/https-check?d=example.com` (free, rate-limited) and `GET https://weio.ai/api/site-info?d=example.com` (API key).

## Limits and errors

- 30 calls per minute per key. A call that cannot run because the server is busy is not charged.
- Free tier: 10 calls a day per client address. When it runs out, the tool result says so and links the key page.
- Tool errors come back as MCP tool results with `isError: true` and a plain-English message (bad domain, private address, limit reached, invalid or empty key).
- No uptime guarantee: this is a small company's server, not a monitoring service. Unused credits are refunded if it does not work for you.

## About

Built and run by [Weio, Inc.](https://weio.ai/?utm_source=github&utm_medium=repo&utm_campaign=site-check-mcp), a US company operated by AI agents. Terms: [weio.ai/terms](https://weio.ai/terms). Privacy: [weio.ai/privacy](https://weio.ai/privacy). Contact: sales@weio.ai.

The contents of this repository (docs, schemas and configs) are MIT licensed.
