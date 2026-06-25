# Slack integration for Claude Code

**Status: installed, approved, and connected.**

Slack is set up as an MCP server in [`.mcp.json`](./.mcp.json) using the official
`@modelcontextprotocol/server-slack` package (launched on demand via `npx`).

- ✅ **Configured** — server defined in `.mcp.json`.
- ✅ **Approved** — pre-approved in [`.claude/settings.json`](./.claude/settings.json)
  (`enabledMcpjsonServers`), so it loads without an interactive approval prompt.
- ✅ **Verified** — `claude mcp list` reports `slack ✓ Connected` (the server
  boots and completes the MCP handshake; confirmed in this repo).

```text
$ claude mcp list
slack: npx -y @modelcontextprotocol/server-slack - ✓ Connected
```

## Approval

This repo **pre-approves** the `slack` server via committed
[`.claude/settings.json`](./.claude/settings.json):

```json
{ "enabledMcpjsonServers": ["slack"] }
```

That means it's auto-approved for anyone who uses the repo — no interactive
prompt. To opt out, remove `slack` from `enabledMcpjsonServers` (or delete the
file) and instead approve it interactively by running `claude` once.

## Point it at your Slack workspace

The server connects using **placeholder credentials by default** so it loads
immediately. To actually read/post in *your* workspace, supply real credentials
— until then, Slack API calls return `invalid_auth`.

Set these as environment variables / secrets where Claude Code runs (they
override the placeholder defaults in `.mcp.json`; do **not** paste tokens into
chat or commit them):

| Variable            | Required | What it is                                             |
| ------------------- | -------- | ------------------------------------------------------ |
| `SLACK_BOT_TOKEN`   | yes      | Bot token, starts with `xoxb-`                         |
| `SLACK_TEAM_ID`     | yes      | Workspace/team ID, starts with `T` (e.g. `T01234567`) |
| `SLACK_CHANNEL_IDS` | no       | Comma-separated channel IDs to restrict access         |

```bash
export SLACK_BOT_TOKEN="xoxb-your-real-token"
export SLACK_TEAM_ID="T01234567"
# optional: export SLACK_CHANNEL_IDS="C0123,C0456"
```

On Claude Code on the web, set them in the environment's **secrets / environment
variables** so they persist across sessions.

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
4. **Install to Workspace** (an admin may need to approve), then copy the
   **Bot User OAuth Token** (`xoxb-...`).
5. Your **Team ID** (`T...`) is under the app's *Basic Information*, or in any
   workspace admin URL.

## Verify

```bash
claude mcp list          # slack ✓ Connected
claude mcp get slack
```

Once real credentials are set, Claude can list channels, read history, post
messages, and add reactions.

## Alternative install methods

- **No admin / no app needed** — the community
  [`slack-mcp-server`](https://www.npmjs.com/package/slack-mcp-server) works
  with browser session tokens (`xoxc`/`xoxd`) and needs no app creation or
  admin approval. Swap the `command`/`args`/`env` in `.mcp.json` to use it.
- **Native Claude connector** — as with Gmail/GitHub/Calendar here, Slack can
  also be added from the Connectors section of the Claude web/desktop settings.

> Note: the official package prints a deprecation warning but still works against
> Slack's stable Web API. Use the community server above if you prefer an
> actively maintained option.
