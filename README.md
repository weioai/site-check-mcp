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

## Agents can buy a key themselves (MPP)

An agent does not need a person to go through a checkout page. `POST https://weio.ai/api/agent/credits/100` ($1, 100 calls) or `POST https://weio.ai/api/agent/credits/1000` ($9, 1,000 calls) answers `402 Payment Required` with a [Machine Payments Protocol](https://mpp.dev) challenge (Stripe, Shared Payment Token, card). Retry with the payment credential and the response carries a `wk_` key, the credits and an itemized receipt. For example, with Link's agent wallet:

```bash
npx @stripe/link-cli mpp pay https://weio.ai/api/agent/credits/100 -X POST
```

Payments settle to Weio, Inc. through Stripe. Stablecoin payment is not offered. Discovery document: [`https://weio.ai/openapi.json`](https://weio.ai/openapi.json).

## Use the same checks through your existing platform account

These routes are alternatives for teams that already use RapidAPI or Apify. They bill through that platform's account and never send you to a Weio checkout. They are separate services from the hosted `weio.ai/mcp` server above.

**RapidAPI MCP.** RapidAPI makes each of these APIs available as MCP tools at `https://mcp.rapidapi.com`. Use your own RapidAPI key and plan; see each API's pricing page for current plan terms.

```bash
# Claude Code: repeat with any host in the list below.
claude mcp add --transport http rapidapi-site-check https://mcp.rapidapi.com \
  --header "x-api-host: website-facts-and-https-check.p.rapidapi.com" \
  --header "x-rapidapi-key: YOUR_RAPIDAPI_KEY"

# Desktop clients that run a local stdio bridge, such as Cursor:
npx mcp-remote https://mcp.rapidapi.com \
  --header "x-api-host: website-facts-and-https-check.p.rapidapi.com" \
  --header "x-rapidapi-key: YOUR_RAPIDAPI_KEY"
```

Choose one `x-api-host`: [`website facts + HTTPS check`](https://rapidapi.com/weioinc/api/website-facts-and-https-check), [`domain and email security`](https://rapidapi.com/weioinc/api/domain-email-security-spf-dmarc-mx-whois), [`website technology detector`](https://rapidapi.com/weioinc/api/website-technology-detector-cms-ecommerce-analytics), or [`website contact details extractor`](https://rapidapi.com/weioinc/api/website-contact-details-extractor-emails-phones-socials). Their host values are respectively `website-facts-and-https-check.p.rapidapi.com`, `domain-email-security-spf-dmarc-mx-whois.p.rapidapi.com`, `website-technology-detector-cms-ecommerce-analytics.p.rapidapi.com`, and `website-contact-details-extractor-emails-phones-socials.p.rapidapi.com`.

**Apify MCP.** Apify exposes each actor as tools through `https://mcp.apify.com`; use an Apify API token from your own account. Pay-per-event charges, if an actor has them, are billed by Apify to that account.

```bash
claude mcp add --transport http apify-weio-tech-stack \
  'https://mcp.apify.com?tools=weio/website-tech-stack-detector' \
  --header "Authorization: Bearer YOUR_APIFY_TOKEN"

npx mcp-remote 'https://mcp.apify.com?tools=weio/website-tech-stack-detector' \
  --header "Authorization: Bearer YOUR_APIFY_TOKEN"
```

Available Weio actor tool routes: `weio/hacked-website-seo-spam-checker`, `weio/local-business-website-lead-qualifier`, `weio/local-business-website-audit`, `weio/website-contact-details-extractor`, `weio/website-tech-stack-detector`, `weio/domain-whois-dns-email-security-checker`, `weio/lighthouse-mobile-median-audit`, `weio/broken-link-checker-bulk`, `weio/website-screenshot-phone-desktop`, and `weio/website-to-pdf-bulk`.

## Install as a plugin or extension

**Claude Code plugin** (MCP server plus a skill that tells Claude when to use each tool; asks for an optional key, stored in your keychain):

```
/plugin marketplace add weioai/site-check-mcp
/plugin install weio-site-check@weio
```

**VS Code (GitHub Copilot), Copilot CLI and Kiro:** an [Agent Plugins](https://agent-plugins.org) package lives in [`agent-plugin/`](agent-plugin). In Kiro: Powers > Add Custom Power > Import power from GitHub > `https://github.com/weioai/site-check-mcp/tree/main/agent-plugin`. To add only the server to VS Code: [install link](vscode:mcp/install?%7B%22name%22%3A%22weio-site-check%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A//weio.ai/mcp%22%7D).

**Gemini CLI extension** (with the same skill as context in [`GEMINI.md`](GEMINI.md)):

```bash
gemini extensions install https://github.com/weioai/site-check-mcp
gemini extensions config weio-site-check   # optional: paste a wk_ key; leave blank for the free tier
```

**Google Antigravity:** plugin manifest in [`antigravity/`](antigravity).

**OpenAI Responses API** (bring your own server; the key is optional):

```json
{ "type": "mcp", "server_label": "weio", "server_url": "https://weio.ai/mcp", "authorization": "wk_...", "require_approval": "never" }
```

**Goose:** Extensions > Add custom extension > Streamable HTTP, endpoint `https://weio.ai/mcp`, optional header `Authorization: Bearer wk_...`.

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

**Gemini CLI** without the extension, in `settings.json`:

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
