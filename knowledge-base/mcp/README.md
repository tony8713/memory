# knowledge-base/mcp/ — per-MCP capability files

One file per MCP server: server name, purpose, tool inventory (READ-safe vs WRITE/mutating clearly marked), real context captured from SAFE read-only calls, and auth/availability caveats. NO secrets (tokens/keys/connection strings) — public addresses/ids only. Write/mutating tools are documented from their schema, never invoked.

For the server-level overview (all connected servers at a glance) see `../mcp-servers.md`.

## Status
| File | Server | Status | Real calls captured |
|---|---|---|---|
| `metro.md` | metro (comms daemon) | **Done** | `list_accounts` (accounts + per-station capabilities) |
| `snapshot.md` | claude.ai Snapshot | **Done** | `snapshot-whoami` (addr `0x257c…E88Ef`), `snapshot-query` (spaces/follows/user) |
| `better-stack.md` | claude.ai Better Stack | **Done** | `list_sources` (mana 1474369), `list_dashboards` (SX Indexers 1041825) |

## TODO (next batches)
- Zapier — `list_enabled_zapier_actions`
- Sentry — `find_organizations` / `find_projects`
- Tenderly — `list_projects`
- Notion — `notion-get-users` / `notion-search` (read-only)
- Gmail — `list_labels` (read-only)
- Google Calendar/Drive — `list_calendars` / `list_recent_files` (read-only)
- Snapshot_MySQL — `mysql_list_tables` (read-only)
- Then: Fireflies, Intercom, Netlify, Cloudflare, Slash, Browserbase
