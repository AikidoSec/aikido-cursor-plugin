---
name: setup
description: Configures the Aikido plugin by setting up the API key and verifying the MCP server. Accepts an optional API key argument to configure automatically. Use when the user wants to set up or verify the Aikido plugin, after installing it, or when aikido_full_scan fails or is unavailable.
arguments:
  - name: aikido-api-key
    description: Your Aikido API key from https://app.aikido.dev → Settings → Integrations → IDE Plugins. When provided, the key is configured automatically.
    required: false
---

The Aikido MCP server is registered by the plugin's bundled `mcp.json`, which references the API key via `${env:AIKIDO_API_KEY}` substitution. Setup's job is to get that env var into the environment Cursor inherits, then verify.

## Step 1: Verify Node.js

Run `node --version`.

- If the command fails, Node.js is missing — tell the user to install Node.js 18.19.0+ from https://nodejs.org and stop.
- If the version is below 18.19.0, tell the user to upgrade (show current version) and stop.
- Otherwise, continue.

## Step 2: Set the `AIKIDO_API_KEY` environment variable (only if a key was provided)

If no API key was passed, skip to Step 3.

Goal: `AIKIDO_API_KEY` should be present in the environment Cursor inherits, so the substitution in the plugin's bundled `mcp.json` resolves at MCP-server startup.

Use your judgment to set it persistently on the user's system. Detect their platform and shell, then edit the appropriate shell rc file (or use `setx` on Windows). Make the edit idempotent — if the variable is already set there, replace it; don't append duplicates.

After setting the variable, tell the user what file you edited and that they need to restart Cursor for the MCP server to pick up the key. Do not continue or take any other steps until after the restart has happened.

## Step 3: Verify

Verify that the aikido MCP server is running and it's tools are discoverable.

- Success → confirm the plugin is configured and ready. Stop.
- Failure → if a key was provided in Step 2 and the user has already restarted Cursor, go to Step 4. If no key was provided, tell the user to get their key from **https://app.aikido.dev** → Settings → Integrations → IDE Plugins and re-run `/aikido:setup <key>`.

## Step 4: Fallback — edit the plugin's bundled `mcp.json`

If verification fails after the user has restarted Cursor, Cursor isn't picking up the env var from the user's shell environment. Offer to fall back to writing the key directly into the plugin's bundled `mcp.json`:

1. Explain the trade-off: the key will be stored in plaintext inside the plugin's install directory, and **will be lost when the plugin is updated or reinstalled** — they'll need to re-run setup at that point.
2. If the user agrees:
   - Locate the plugin's bundled `mcp.json` (in the plugin install directory; ask the user for the path if you can't determine it).
   - Replace `"AIKIDO_API_KEY": "${env:AIKIDO_API_KEY}"` with `"AIKIDO_API_KEY": "<literal key>"`.
3. Have the user restart Cursor and re-run the verification scan.
