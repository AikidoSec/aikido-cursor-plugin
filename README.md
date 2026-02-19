# Aikido Plugin for Cursor

A lightweight Model Context Protocol (MCP) server that exposes Aikido’s Code and Secrets Scan as a tool for AI coding agents and IDEs. It lets your agent scan code and returns machine-readable findings you can triage or fix. Combine with the [Aikido Cursor IDE Plugin](https://help.aikido.dev/ide-plugins/supported-ides/cursor-ide) to enable full workspace and real-time scanning, AI autofixes and more.

## Configuration

### API Key Setup

The MCP server requires an Aikido API key to authenticate with the Aikido API. First, [generate your key](https://app.aikido.dev/settings/integrations/ide/cursor). Then you can provide it to the MCP server in two ways:

1. **Via MCP Configuration (Recommended)**: Set the `AIKIDO_API_KEY` environment variable in your MCP configuration file
2. **Via System Environment Variable**: Set `AIKIDO_API_KEY` as a system environment variable

## Documentation

- [Aikido MCP server](https://help.aikido.dev/ide-plugins/aikido-mcp)
- [Aikido Cursor IDE Plugin](https://help.aikido.dev/ide-plugins/supported-ides/cursor-ide)
