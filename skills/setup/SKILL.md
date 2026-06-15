---
name: setup
description: Configures the Aikido plugin by signing the user in through the MCP login tool and verifying the MCP server. Use when the user wants to set up or verify the Aikido plugin, after installing it, when aikido mcp tool call fails or is unavailable, or when the user wants to switch Aikido accounts or re-authenticate.
---

When helping the user configure the Aikido security plugin:

## First: Verify Node.js version

Before doing anything else, run `node --version` to check the installed Node.js version.

- If Node.js is not installed or the command fails, stop and tell the user that Node.js 18.19.0 or higher is required to run the Aikido MCP server, and direct them to https://nodejs.org to install it.
- If the version is below 18.19.0, stop and tell the user that the Aikido MCP server requires Node.js 18.19.0 or higher, show the currently installed version, and direct them to https://nodejs.org to upgrade.
- If the version is 18.19.0 or higher, proceed with the steps below.

**If the user is asking to switch accounts or re-authenticate**, call **aikido:aikido_login** with `force_reauth: true` and skip step 1 — the tool will always return fresh sign-in URLs. Continue from step 3 to walk the user through opening the URL and step 4 to verify.

**Otherwise**, follow the standard setup flow:

1. Check the MCP server is reachable and the user is signed in by calling **aikido:aikido_login** with no arguments. The tool is idempotent — it returns "Already signed in" if a valid token is cached, otherwise it starts a new sign-in flow.
2. If it reports the user is already signed in, confirm to the user that the Aikido plugin is configured and ready to use. Stop here.
3. If it returns region-specific sign-in URLs (EU / US / ME), present them to the user verbatim — do not strip or modify the `state` or `redirect_uri` query parameters — and ask them to open the URL for their region in a browser to complete sign-in.
4. Once the user reports they have completed the browser sign-in, verify the setup by calling **aikido:aikido_login** again.
5. If the **aikido_login** call itself fails because the MCP server is unavailable, tell the user to ensure the Aikido plugin is installed and that Cursor has loaded the `aikido` MCP server, then retry.
