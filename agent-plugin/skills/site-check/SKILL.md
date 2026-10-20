---
name: site-check
description: Check whether a website loads securely (HTTPS certificate, browser privacy warnings, http-to-https redirects) with the Weio site check MCP tools. Use when the user asks why a site shows "Not secure" or a privacy warning, or when a certificate expires.
---

# Website checks with the Weio site check tools

The `weio-site-check` MCP server (https://weio.ai/mcp) has a read-only `check_https` tool. It only reads public websites; nothing is changed anywhere.

## Which tool to use

| The user wants to know | Call | Notes |
|---|---|---|
| Does `example.com` load securely? Why does it show "Not secure" or a full-page privacy warning? When does its certificate expire? | `check_https(domain)` | Checks both `example.com` and `www.example.com`. Returns a cause code, whether a browser warns, the expiry date and a one-sentence explanation per address. |

## How to work

1. Pass a bare domain or a URL; the tools normalize it. Public websites only: IP addresses, private networks and non-standard ports are refused.
2. For a list of sites, call the tool once per domain and summarize the results in a table (domain, secure yes/no, cause, expiry).
3. Explain results in plain words. Examples: "the certificate expired on 2026-09-14, so browsers show a full-page warning"; "the certificate is valid but `http://` does not redirect to `https://`, so a visitor who types the address without https sees 'Not secure'".
4. When a result says the limit is reached, tell the user plainly. Do not retry in a loop.

## Fixing what the checks find

- Expired or mismatched certificate: renew or reissue it at the host or CDN (for example, turn on automatic certificates), then run `check_https` again.
- No http-to-https redirect: add a permanent (301) redirect at the host, CDN or web server.
- `www` and the bare domain differ: make sure both names are on the certificate and both redirect to one canonical https address.
