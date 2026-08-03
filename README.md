<h1 align="center">Reply.io MCP Server</h1>

<p align="center">
  Connect Reply.io to your AI client and let it work across your team and workspace.
  <br />
  <a href="https://reply.io">reply.io</a> · <a href="https://docs.reply.io/reply-mcp">Documentation</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/transport-Streamable_HTTP-green" alt="Transport" />
  <img src="https://img.shields.io/badge/license-MIT-brightgreen" alt="License" />
</p>

---

## Overview

A remote MCP server at `mcp.reply.io` that lets MCP-compatible AI clients — Claude, ChatGPT, Cursor, Codex, and more — work with your Reply.io CRM and AI SDR. One connection gives an agent a catalog of tools spanning contacts, sequences, inbox, tasks, analytics, and the AI SDR ("Jason"), so it can read and act on Reply data without making direct HTTP requests. The tools run the same logic as the Reply.io API and the in-app AI assistant.

No local installation required. Free for all Reply.io users.

📖 **Full documentation:** [docs.reply.io/reply-mcp](https://docs.reply.io/reply-mcp)

## Where this fits

Reply's agentic toolkit is three pieces. They are complementary, not alternatives:

| | What it is | When you want it |
|---|---|---|
| **[reply CLI](https://github.com/reply-team/reply-cli)** | `npm i -g reply-cli` — authenticated access to *every* v3 endpoint via `reply api` | Your agent has a shell. This is the complete surface. |
| **Reply MCP** (this repo) | A curated tool catalog over `mcp.reply.io` | Your client speaks MCP and has no shell — desktop apps, hosted assistants. |
| **[reply-skills](https://github.com/reply-team/reply-skills)** | Outbound expertise as markdown skills: what to do, in what order, with what guardrails | Your agent knows *how* to call things but not *what* to run. |

MCP gives your agent tools. Skills give it judgement. Most setups want both; a shell-capable
agent can do everything through the CLI alone.

The shortest path that works: install the CLI, `reply auth login`, `reply skills install`, then
talk to your agent in plain words. MCP is optional — add it when your client has no shell.

## Connect

Reply MCP is a remote server at `https://mcp.reply.io`. Authenticate with a **scoped personal API key** (recommended) or **OAuth**, then add it to your client.

**Claude Code**

```bash
claude mcp add --transport http reply-mcp https://mcp.reply.io \
  --header "Authorization: Bearer usk_...your_key..."
```

**Desktop apps (Claude, ChatGPT)**

Add Reply MCP as a remote / custom connector using the URL `https://mcp.reply.io` and authorize with OAuth.

Create and scope a key under **Settings → API Key** in your [Reply.io dashboard](https://run.reply.io).

> **Note:** Passing credentials in the URL (for example `?api_key=...`) is **not** supported. Always authenticate with the `Authorization` header or OAuth.

- **Full connection guide** (Codex, `mcp-remote` proxy, OAuth): [docs.reply.io/reply-mcp#connect](https://docs.reply.io/reply-mcp#connect)
- **Authentication & keys:** [docs.reply.io/reply-mcp#authentication](https://docs.reply.io/reply-mcp#authentication)

## Authentication & scopes

Reply MCP authenticates as a single Reply.io user, via one of two methods:

- **Personal API key** (recommended) — passed as `Authorization: Bearer <token>`. A personal key is **scoped**: you grant it only the permissions your agent needs, and it can call only the tools those permissions cover.
- **OAuth** — authorize directly from the client, no key to copy. OAuth grants the **full tool catalog** (all scopes).

Each tool requires a single scope such as `contacts:read` or `sequences:operate`; a key can call a tool only if it holds that scope. Act-on-behalf keys (**Team** and **Organization** keys) are not supported on this single-user connection.

See the full scope list at [docs.reply.io/api-reference/authentication#scopes](https://docs.reply.io/api-reference/authentication#scopes).

## Tools

71 tools across your CRM and AI SDR. **⚠️ marks destructive tools** (create / update / delete / send / assign) — these require the matching `:write` or `:operate` scope and carry a `destructiveHint` so conforming clients can prompt for confirmation. Call arguments (the JSON input schema) are advertised live via `tools/list`; the tables below describe what each tool is *for*.

### Contacts & lists

| Tool | Purpose |
|------|---------|
| `reply_search_contacts` | Look up contacts by exact email or LinkedIn URL |
| `reply_filter_contacts` | List contacts by list/sequence membership or a name/email/company search |
| `reply_get_contact_activity` | Get a contact's activity history (emails, LinkedIn, calls, events) |
| ⚠️ `reply_create_contact` | Create a contact |
| ⚠️ `reply_update_contact` | Patch fields on an existing contact |
| ⚠️ `reply_change_contact_owner` | Reassign contacts to another team member |
| ⚠️ `reply_mark_contacts_as_replied` | Flag/unflag contacts as replied (stops automated outreach) |
| ⚠️ `reply_blacklist_contact` | Blacklist a contact's email or whole domain |
| `reply_search_lists` | Resolve contact lists to their numeric IDs by name |

### Sequences

| Tool | Purpose |
|------|---------|
| `reply_search_sequences` | Find sequences by name, status, or archive state |
| `reply_list_schedules` | List sending schedules (business-hours windows) |
| `reply_get_sequence_steps` | Get a sequence's ordered steps (type, delay, variant count) |
| `reply_get_sequence_step_variants` | Get a step's A/B content variants |
| ⚠️ `reply_change_status_in_sequence` | Set a contact's status within a sequence |
| ⚠️ `reply_add_contact_to_sequence` | Enroll contacts into a sequence |
| ⚠️ `reply_start_sequence` | Start or resume a sequence |
| ⚠️ `reply_pause_sequence` | Pause a sequence |
| ⚠️ `reply_assign_email_account_to_sequence` | Attach an email account (mailbox) to a sequence |
| ⚠️ `reply_assign_linkedin_account_to_sequence` | Attach a LinkedIn account to a sequence |
| ⚠️ `reply_assign_schedule_to_sequence` | Set a sequence's sending schedule |

### Analytics

| Tool | Purpose |
|------|---------|
| `reply_get_sequence_stats` | Email + LinkedIn performance for one sequence, over any date range |
| `reply_compare_sequence_performance` | Compare performance across multiple sequences side by side |

### Inbox

| Tool | Purpose |
|------|---------|
| `reply_get_inbox_emails` | List inbox threads (email + LinkedIn) with short previews |
| ⚠️ `reply_send_inbox_reply` | Send a reply on an existing inbox thread |
| ⚠️ `reply_change_inbox_category` | Set or clear a thread's category |

### Tasks

| Tool | Purpose |
|------|---------|
| `reply_list_my_tasks` | List your tasks, filtered by type, status, due window, or contact |
| ⚠️ `reply_create_task` | Create a manual task |
| ⚠️ `reply_complete_task` | Mark a task as completed |

### Channels & accounts

| Tool | Purpose |
|------|---------|
| `reply_list_email_accounts` | List workspace email accounts (status, daily limit, tags) |
| `reply_list_linkedin_accounts` | List connected LinkedIn accounts (status, health, tier) |

### Team

| Tool | Purpose |
|------|---------|
| `reply_search_team_members` | Resolve teammates to their numeric user IDs by name or email |

### Help & workspace

| Tool | Purpose |
|------|---------|
| `reply_diagnose` | Explain why a sequence isn't sending and how to fix it |
| `reply_get_app_map` | Catalog of Reply.io app areas and what the assistant can do in each |
| `reply_search_knowledge_base` | Search the Reply.io Help Center for how-to answers |
| `reply_get_knowledge_base_article` | Read a full Help Center article by slug |
| `reply_report_unsupported_request` | Report a missing capability to the product team |

### AI SDR — Offers

| Tool | Purpose |
|------|---------|
| `reply_list_offers` | List AI SDR offers (what Jason can pitch) |
| `reply_get_offer` | Get one offer in full |
| ⚠️ `reply_create_offer` | Create a new offer |
| ⚠️ `reply_update_offer` | Patch an existing offer |
| ⚠️ `reply_attach_offer_to_sequence` | Attach an offer to an AI SDR sequence |
| `reply_generate_offer_from_website` | Draft offer content from a company website (review only, saves nothing) |

### AI SDR — Playbooks

| Tool | Purpose |
|------|---------|
| `reply_list_playbooks` | List playbooks (named sets of selling instructions) |
| `reply_get_playbook` | Get one playbook in full |
| ⚠️ `reply_create_playbook` | Create a new playbook |
| ⚠️ `reply_update_playbook` | Patch an existing playbook |
| ⚠️ `reply_duplicate_playbook` | Duplicate a playbook |
| ⚠️ `reply_attach_playbook_to_sequence` | Attach a playbook to an AI SDR sequence |

### AI SDR — Knowledge bases

| Tool | Purpose |
|------|---------|
| `reply_list_knowledge_bases` | List AI SDR knowledge bases |
| `reply_get_knowledge_base` | Get one knowledge base in full (with URL sources) |
| ⚠️ `reply_create_knowledge_base` | Create a knowledge base |
| ⚠️ `reply_update_knowledge_base` | Rename or set a knowledge base's instructions |
| ⚠️ `reply_add_knowledge_base_source` | Add a web-page URL as a source |
| ⚠️ `reply_delete_knowledge_base_source` | Remove a URL source |
| ⚠️ `reply_attach_knowledge_base_to_sequence` | Attach a knowledge base to an AI SDR sequence |

### AI SDR — Reply handlers

| Tool | Purpose |
|------|---------|
| `reply_list_reply_handlers` | List reply handlers in a knowledge base |
| `reply_get_reply_handler` | Get one reply handler in full |
| ⚠️ `reply_create_reply_handler` | Add a reply handler (how Jason answers a kind of question) |
| ⚠️ `reply_update_reply_handler` | Patch a reply handler |
| ⚠️ `reply_delete_reply_handler` | Remove a reply handler |

### AI SDR — Re-engagement cards

| Tool | Purpose |
|------|---------|
| `reply_list_reengagement_cards` | List re-engagement cards in a knowledge base |
| `reply_get_reengagement_card` | Get one re-engagement card in full |
| ⚠️ `reply_create_reengagement_card` | Add a re-engagement card (how Jason wins back a quiet prospect) |
| ⚠️ `reply_update_reengagement_card` | Patch a re-engagement card |
| ⚠️ `reply_delete_reengagement_card` | Remove a re-engagement card |

### AI SDR — Approvals & reply mode

| Tool | Purpose |
|------|---------|
| `reply_list_pending_approvals` | List AI SDR draft messages waiting for approval |
| ⚠️ `reply_approve_message` | Approve a draft and send it immediately |
| ⚠️ `reply_bulk_approve_messages` | Approve and send multiple drafts at once (atomic) |
| ⚠️ `reply_regenerate_message` | Ask the AI SDR to rewrite a pending draft |
| ⚠️ `reply_reject_message` | Reject a draft and remove the contact from that sequence |
| ⚠️ `reply_set_sequence_reply_mode` | Switch a sequence between Review and Autonomous reply mode |

## Example prompts

Once connected, you can ask your AI assistant things like:

- "List my active sequences and how each is performing."
- "Add john@acme.com to the Demo Follow-up sequence."
- "Pause the Cold Outreach sequence — we're updating the copy."
- "Why isn't my Q2 Launch sequence sending?"
- "Show me the AI SDR drafts waiting for approval and approve the good ones."
- "Compare step-by-step performance for my top two sequences."
- "Create an offer for our new onboarding service and attach it to the Q3 sequence."

## Learn more

- [Reply MCP documentation](https://docs.reply.io/reply-mcp) — capabilities, authentication, and connection guide.
- [Connecting Reply MCP to your AI model](https://support.reply.io/en/articles/12955018-connecting-reply-mcp-to-your-ai-model) — Help Center walkthrough.
- [Reply MCP overview](https://reply.io/mcp/)
- [reply CLI](https://github.com/reply-team/reply-cli) — the same Reply.io surface from a terminal, including every v3 endpoint via `reply api`.
- [reply-skills](https://github.com/reply-team/reply-skills) — outbound expertise as installable skill packs for your agent.
- [agents.reply.io](https://agents.reply.io) — how the CLI, MCP and the skills fit together, and which interface to pick.

## License

MIT — see [LICENSE](LICENSE).
