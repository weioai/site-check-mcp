# Weio site check: Agent Plugin

[Agent Plugins 1.0](https://agent-plugins.org) package for VS Code (GitHub Copilot), Copilot CLI and Kiro. It registers the hosted Weio site check MCP server (`https://weio.ai/mcp`) and a skill that explains when to use its three read-only tools (`check_https`, `site_info`, `find_businesses`). Without a key the server allows 10 calls a day; to use a paid key, add an `Authorization: Bearer wk_...` header in your own client's MCP configuration. Full details: [main README](../README.md).
