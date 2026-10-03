# Calling the server with curl

The endpoint is stateless, so a single POST per call works; `initialize` is optional for curl use.

```bash
# list the tools
curl -s https://weio.ai/mcp -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'

# public business facts from a homepage
curl -s https://weio.ai/mcp -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"site_info","arguments":{"domain":"example.com"}}}'

# with a paid key
curl -s https://weio.ai/mcp -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
  -H 'Authorization: Bearer wk_...' \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"check_https","arguments":{"domain":"example.com"}}}'
```

Each result has a human-readable `content[0].text` and a machine-readable `structuredContent` object.
