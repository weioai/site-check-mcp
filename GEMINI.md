# Weio site check

This extension connects Gemini CLI to Weio's hosted MCP server at https://weio.ai/mcp. This extension uses its read-only `check_https` tool, which works on public websites only.

- `check_https(domain)`: does the site load securely for `example.com` and `www.example.com`; if not, why (expired certificate, name mismatch, self-signed, no HTTPS, redirect problems, unreachable); certificate expiry date; one-sentence explanation.

Guidance:
- Call `check_https` when the user asks why a site shows "Not secure" or a privacy warning, or when a certificate expires. For several domains, call once per domain and summarize in a table.
- If a result says the limit is reached, tell the user and do not retry.
