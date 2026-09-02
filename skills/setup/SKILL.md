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

**If the user is asking to switch accounts or re-authenticate**, call **aikido:aikido_login** with `force_reauth: true` and skip step 1 — the tool will always return a fresh sign-in URL. Continue from step 3 to walk the user through opening the URL and step 4 to verify.

**Otherwise**, follow the standard setup flow:

1. Check the MCP server is reachable and the user is signed in by calling **aikido:aikido_login** with no arguments. The tool is idempotent — it returns "Already signed in" if a valid token is cached, otherwise it starts a new sign-in flow.
2. If it reports the user is already signed in, confirm to the user that the Aikido plugin is configured and ready to use. Stop here.
3. If it returns a sign-in URL, present it to the user verbatim — do not strip or modify the `state`, `redirect_uri`, or `regionRedirect` query parameters — and ask them to open it in a browser to complete sign-in. The URL automatically redirects the user to their account's region, so there is no need to pick a region. Only pass the optional `region` argument when the user explicitly names a region (e.g. EU / US / ME / AU), or when the automatic redirect sent them to the wrong one.
4. Once the user reports they have completed the browser sign-in, verify the setup by calling **aikido:aikido_login** again.
5. If the **aikido_login** call itself fails because the MCP server is unavailable, first check whether it is the known Cursor npm-prefix startup crash (see below) and repair it. If it is not that issue, tell the user to ensure the Aikido plugin is installed and that Cursor has loaded the `aikido` MCP server, then retry.

## Repairing the Cursor npm-prefix startup crash

On some machines the `aikido` MCP server fails to start under Cursor with an `npm error ... ENOENT ... lstat` on a path **inside the Cursor app bundle**, e.g. `/Applications/Cursor.app/Contents/Resources/app/resources/lib`. This happens because Cursor launches the server with its own bundled Node, so `npx`/`npm` derives its global prefix from a path inside the app bundle and crashes. When you detect this, repair it automatically rather than just asking the user to reinstall.

### 1. Confirm it is this issue

Only apply the fix below when the symptoms match — do not touch config otherwise.

- Look for the signature in the most recent npm debug logs:
  `ls -t "$HOME/.npm/_logs/"*-debug-0.log | head -3` then grep them for `Cursor.app` together with `ENOENT`/`lstat`.
- The user may also paste the error from Cursor's Output panel (the `MCP: user-aikido` channel); the tell-tale line is `npm error path .../Cursor.app/Contents/Resources/...lib`.
- Supporting signal: run `command -v npx` and `npm config get prefix`. If Node is managed by a version manager (nvm, fnm, asdf) or lives somewhere Cursor's GUI process won't inherit on `PATH`, this crash is likely.

If the signature is absent, this is a different problem — stop and fall back to the generic "ensure the plugin is installed" guidance.

### 2. Compute the correct, machine-specific values

Run these in the user's shell (they return the *real* values, not Cursor's):

- Real npm prefix: `npm config get prefix` → e.g. `/opt/homebrew` or `/Users/<you>/.nvm/versions/node/<v>`.
- Verify it is usable: `ls -d "$(npm config get prefix)/lib"` must succeed (npm requires `<prefix>/lib` to exist).
- Real npx path (fallback fix): `command -v npx`.

### 3. Locate the active mcp.json

Find the file that currently defines the `aikido` server (the entry whose args include `@aikidosec/mcp`). Check, in order:

- the project config: `.cursor/mcp.json` in the open workspace,
- the global config: `~/.cursor/mcp.json`,
- the installed plugin's bundled `mcp.json`.

### 4. Apply the fix (ask before editing)

Editing `mcp.json` is a persistent configuration change, so briefly tell the user what you are about to change and get a yes first. Then, **preferring the environment-variable fix**, merge an `env` block into the `aikido` server entry using the prefix from step 2:

```json
"aikido": {
  "command": "npx",
  "args": ["-y", "@aikidosec/mcp@1.0.21"],
  "env": { "NPM_CONFIG_PREFIX": "<value of `npm config get prefix`>" }
}
```

If a follow-up attempt still crashes, use the **absolute-npx fix** instead: set `"command"` to the full path from `command -v npx` (keep `args` unchanged).

Write the literal resolved paths — do not leave `<...>` placeholders or shell substitutions in the JSON. If the file that defines the server is the plugin's bundled `mcp.json`, note to the user that a plugin update may reset it, and re-running `/aikido:setup` will reapply the fix.

### 5. Reload and verify

Tell the user to fully quit and reopen Cursor (MCP config is read at startup) and then re-run `/aikido:setup`. Confirm the repair by calling **aikido:aikido_login** again.
