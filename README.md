# Aikido Plugin for Cursor

A lightweight Model Context Protocol (MCP) server that exposes Aikido Code and Secrets Scan as a tool for AI coding agents and IDEs. It enables your agent to scan code and returns machine readable findings you can triage or fix automatically.

In combination with the [Aikido Cursor IDE Plugin](https://help.aikido.dev/ide-plugins/supported-ides/cursor-ide), you get real time feedback as you code, one click AI autofixes, full workspace scanning and rich security context directly inside your editor.

## Configuration

### API Key Setup

The MCP server requires an Aikido API key to authenticate with the Aikido API. Start by [generating your key](https://app.aikido.dev/settings/integrations/ide/cursor). Then provide it to the MCP server using one of the following methods:

1. **Via MCP Configuration (Recommended)**: Set the `AIKIDO_API_KEY` environment variable in your MCP configuration file
2. **Via System Environment Variable**: Set `AIKIDO_API_KEY` as a system environment variable

## Documentation

- [Aikido MCP server](https://help.aikido.dev/ide-plugins/aikido-mcp)
- [Aikido Cursor IDE Plugin](https://help.aikido.dev/ide-plugins/supported-ides/cursor-ide)
