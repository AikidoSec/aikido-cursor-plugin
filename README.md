# Aikido Plugin for Cursor

A lightweight Model Context Protocol (MCP) server that exposes Aikido Code and Secrets Scan as a tool for AI coding agents and IDEs. It enables your agent to scan code and returns machine readable findings you can triage or fix automatically.

In combination with the [Aikido Cursor IDE Plugin](https://help.aikido.dev/ide-plugins/supported-ides/cursor-ide), you get real time feedback as you code, one click AI autofixes, full workspace scanning and rich security context directly inside your editor.

## Configuration

### Sign in

The MCP server authenticates via a browser-based sign-in flow — no API key required. Run the `/aikido:setup` skill (or call the `aikido_login` MCP tool) and follow the region-specific sign-in URL it returns. The server caches your token locally for future sessions; re-run setup with `force_reauth` to switch accounts.

## Documentation

- [Aikido MCP server](https://help.aikido.dev/ide-plugins/aikido-mcp)
- [Aikido Cursor IDE Plugin](https://help.aikido.dev/ide-plugins/supported-ides/cursor-ide)
