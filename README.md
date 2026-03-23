<h1 align="center">Reply.io MCP Server</h1>

<p align="center">
  Access Reply.io's sales engagement platform via the Model Context Protocol.
  <br />
  <a href="https://reply.io">reply.io</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/MCP-1.0-blue" alt="MCP Version" />
  <img src="https://img.shields.io/badge/transport-Streamable_HTTP-green" alt="Transport" />
  <img src="https://img.shields.io/badge/license-MIT-brightgreen" alt="License" />
</p>

---

## Overview

A remote MCP server at `mcp.reply.io` that lets AI assistants work with Reply.io sequences, contacts, and outreach statistics. No local installation required. Free for all Reply.io users.

## Tools

### Sequence Management

| Tool | Description | Parameters |
|------|-------------|------------|
| `reply_list_sequences` | List all sequences with cursor-based pagination | `cursor?` · `limit` (default 50) |
| `reply_get_sequence_details` | Get sequence info and optionally sent statistics | `sequenceId` · `includeSentStats?` |
| `reply_start_sequence` | Start a paused sequence | `sequenceId` |
| `reply_pause_sequence` | Pause a running sequence | `sequenceId` |
| `reply_add_contact_to_sequence` | Add a contact to a sequence (creates if needed) | `sequenceId` · `email` · `firstName?` · `lastName?` · `title?` · `company?` · `phone?` · `linkedIn?` |

### Statistics

| Tool | Description | Parameters |
|------|-------------|------------|
| `reply_get_contact_statistics` | Get statistics for a contact by email | `email` |
| `reply_get_sequence_step_stats` | Get step-level stats (delivery, opens, replies, clicks) | `sequenceId` · `stepId` · `from?` · `to?` |
| `reply_get_sequence_clicks` | Get email link click statistics for a sequence | `sequenceId` |

### Contact Actions

| Tool | Description | Parameters |
|------|-------------|------------|
| `reply_mark_contacts_as_replied` | Mark contacts as replied in a campaign | `prospectIds` · `campaignId?` |

## Example Prompts

Once connected, you can ask your AI assistant things like:

- "List my active sequences"
- "How is my outreach sequence performing? Show me open and reply rates"
- "Add john@acme.com to the Demo Follow-up sequence"
- "Pause the Cold Outreach sequence — we're updating the copy"
- "Which links are getting the most clicks in my onboarding sequence?"
- "Pull up stats for jane@example.com — did she engage with anything?"
- "Start the Q2 Launch sequence back up"
- "Mark these contacts as replied so they stop getting follow-ups"
- "Compare step-by-step performance for my top two sequences"
- "Give me a summary of all my sequences and their current status"

## Setup

This is a remote server — connect directly, no local process needed.

### Authentication

You'll need a Reply.io API key from your [account settings](https://run.reply.io/Dashboard/Material#/settings/apiKey).

Two methods are supported:

1. **API Key header** — `X-API-Key: <your-key>`
2. **Query parameter** — `?api_key=<your-key>`

OAuth with Dynamic Client Registration is also available.

### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "reply": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.reply.io?api_key=YOUR_REPLY_API_KEY"
      ]
    }
  }
}
```

### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "reply": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.reply.io?api_key=YOUR_REPLY_API_KEY"
      ]
    }
  }
}
```

### Windsurf

Add to your MCP configuration:

```json
{
  "mcpServers": {
    "reply": {
      "serverUrl": "https://mcp.reply.io",
      "headers": {
        "X-API-Key": "YOUR_REPLY_API_KEY"
      }
    }
  }
}
```

### Generic MCP Client (Streamable HTTP)

- **URL:** `https://mcp.reply.io`
- **Transport:** Streamable HTTP
- **Auth header:** `X-API-Key: <your-key>`

Or pass the key as a query parameter: `https://mcp.reply.io?api_key=<your-key>`

## License

MIT — see [LICENSE](LICENSE).
