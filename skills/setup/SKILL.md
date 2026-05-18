---
name: setup
description: Configures the Aikido plugin by setting up the API key and verifying the MCP server. Accepts an optional API key argument to configure automatically. Use when the user wants to set up or verify the Aikido plugin, after installing it, or when aikido_full_scan fails or is unavailable.
arguments:
  - name: aikido-api-key
    description: Your Aikido API key from https://app.aikido.dev → Settings → Integrations → IDE Plugins. When provided, the key is configured automatically in Cursor's MCP settings.
    required: false
---

When helping the user configure the Aikido security plugin:

## First: Verify Node.js version

Before doing anything else, run `node --version` to check the installed Node.js version.

- If Node.js is not installed or the command fails, stop and tell the user that Node.js 18.19.0 or higher is required to run the Aikido MCP server, and direct them to https://nodejs.org to install it.
- If the version is below 18.19.0, stop and tell the user that the Aikido MCP server requires Node.js 18.19.0 or higher, show the currently installed version, and direct them to https://nodejs.org to upgrade.
- If the version is 18.19.0 or higher, proceed with the steps below.

## If the user provided an API key as an argument:

1. Read `~/.cursor/mcp.json` (create it as `{ "mcpServers": {} }` if it doesn't exist).
2. Merge the Aikido MCP server entry into `mcpServers`, preserving all other existing servers and settings. The result should include:
   ```json
   {
     "mcpServers": {
       "aikido": {
         "command": "npx",
         "args": ["-y", "@aikidosec/mcp"],
         "env": {
           "AIKIDO_API_KEY": "<key>"
         }
       }
     }
   }
   ```
3. Write the updated JSON back to `~/.cursor/mcp.json`.
4. Confirm to the user that the API key has been saved to their global Cursor MCP settings and will apply to all projects.
5. Inform the user that they need to restart Cursor for the MCP server to pick up the new key.
6. Offer to verify the setup after restart by running **aikido_full_scan** with a test payload.

## If no API key was provided:

1. Check whether the Aikido MCP server is currently available by calling **aikido_full_scan** with a minimal test payload: one file with path `test.js` and content `// test`.
2. If it responds successfully, confirm to the user that the Aikido plugin is already configured and ready to use.
3. If it fails or is unavailable, guide the user through the setup:
   a. Tell the user to get their API key from **https://app.aikido.dev** → Settings → Integrations → IDE Plugins.
   b. Suggest running the skill with the key directly:
      ```
      /aikido:setup <my-key>
      ```
   c. Remind the user to restart Cursor after setting the key so the MCP server picks it up.
   d. Offer to verify the setup after they have set the key by running the test scan again.
