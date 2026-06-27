# knowledge-base/mcp/ — per-MCP capability files

One file per MCP server: server name, purpose, tool inventory (READ-safe vs WRITE/mutating clearly marked), real context captured from SAFE read-only calls, and auth/availability caveats. NO secrets (tokens/keys/connection strings) — public addresses/ids only. Write/mutating tools are documented from their schema, never invoked.

For the server-level overview (all connected servers at a glance) see `../mcp-servers.md`.

## Status
| File | Server | Status | Real calls captured |
|---|---|---|---|
| `metro.md` | metro (comms daemon) | **Done** | `list_accounts` (accounts + per-station capabilities) |
| `snapshot.md` | claude.ai Snapshot | **Done** | `snapshot-whoami` (addr `0x257c…E88Ef`), `snapshot-query` (spaces/follows/user) |
| `better-stack.md` | claude.ai Better Stack | **Done** | `list_sources` (mana 1474369), `list_dashboards` (SX Indexers 1041825) |
| `zapier.md` | claude.ai Zapier | **Done** | `list_enabled_zapier_actions` → none enabled (fronts 9000+ apps incl. Typefully) |
| `sentry.md` | claude.ai Sentry | **Done** | `find_organizations` (snapshot-labs), `find_projects` (16; NO mana) |
| `tenderly.md` | claude.ai Tenderly | **Done** | `list_projects` (1: default), `get_networks` (~100 chains) |
| `google-workspace.md` | Gmail/Calendar/Drive | **Stub** | schema-only (no user data read this tick — privacy) |

## TODO (next batches)
- Notion — `notion-get-users` / `notion-search` (read-only)
- Google Workspace read-onlys — `list_labels` / `list_calendars` / `list_recent_files` (structure only, when warranted)
- Snapshot_MySQL — `mysql_list_tables` (read-only)
- Intercom, Netlify, Fireflies — read-only listings
- Then: Cloudflare, Slash, Browserbase, Docusign, Calendly
