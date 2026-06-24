# Slack integration for Claude Code

Slack is installed as an MCP server in [`.mcp.json`](./.mcp.json). It uses the
official `@modelcontextprotocol/server-slack` package, launched on demand via
`npx`, with credentials read from environment variables (no secrets are stored
in the repo).

Two steps remain to make it live: **(1) provide credentials** and
**(2) approve the server**.

## 1. Provide credentials

The server needs two required values (and one optional):

| Variable            | Required | What it is                                              |
| ------------------- | -------- | ------------------------------------------------------- |
| `SLACK_BOT_TOKEN`   | yes      | Bot token, starts with `xoxb-`                          |
| `SLACK_TEAM_ID`     | yes      | Workspace/team ID, starts with `T` (e.g. `T01234567`)  |
| `SLACK_CHANNEL_IDS` | no       | Comma-separated channel IDs to restrict access          |

### Getting a bot token

1. Go to https://api.slack.com/apps → **Create New App** → *From scratch*.
2. Pick your workspace and name the app.
3. **OAuth & Permissions** → add these **Bot Token Scopes**:
   - `channels:history`
   - `channels:read`
   - `chat:write`
   - `reactions:write`
   - `users:read`
   - `users.profile:read`
4. **Install to Workspace** (a workspace admin may need to approve this), then
   copy the **Bot User OAuth Token** (`xoxb-...`).
5. Find your **Team ID** (`T...`) under the app's *Basic Information*, or from
   any workspace URL / `https://<workspace>.slack.com` admin page.

### Setting the variables

Export them in the environment where Claude Code runs:

```bash
export SLACK_BOT_TOKEN="xoxb-your-token"
export SLACK_TEAM_ID="T01234567"
# optional:
# export SLACK_CHANNEL_IDS="C0123,C0456"
```

On Claude Code on the web, set them as **environment variables / secrets** in
the environment configuration so they persist across sessions.
Do **not** paste the token into chat or commit it.

## 2. Approve the server

Project MCP servers from `.mcp.json` require a one-time approval:

```bash
claude        # launch interactively, then approve the "slack" server when prompted
```

## Verify

```bash
claude mcp list          # slack should show ✓ Connected once creds are set + approved
claude mcp get slack
```

Once connected, Claude can list channels, read history, post messages, and add
reactions.

## Alternative install methods

- **No admin / no app needed** — the community
  [`slack-mcp-server`](https://www.npmjs.com/package/slack-mcp-server) works
  with browser session tokens (`xoxc`/`xoxd`) and needs no app creation or
  admin approval. Swap the `command`/`args`/`env` in `.mcp.json` if you prefer
  this route.
- **Native Claude connector** — the same way Gmail/GitHub/Calendar are wired up
  here, you can add Slack from the Connectors section of the Claude web/desktop
  settings instead of via `.mcp.json`.
