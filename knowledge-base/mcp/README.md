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
| `notion.md` | claude.ai Notion | **Done** | `notion-get-teams` (General live; topical spaces archived), `notion-get-users` (6 people incl. Fabien + bots), `notion-search` |
| `intercom.md` | claude.ai Intercom | **Done** | `list_articles` (59 articles, workspace `n9dmxtcs`, help.snapshot.box) |
| `netlify.md` | claude.ai Netlify | **Done** | `get-user` (Tony), `get-teams` (tony8713/stagelabs/snapshotlabs), `get-projects` (SX 11 sites + Stage 2 sites) |
| `fireflies.md` | claude.ai Fireflies | **Done** | `fireflies_get_user` (Tony, admin, 0 min) — schema-only otherwise (PII) |
| `snapshot-mysql.md` | claude.ai Snapshot_MySQL | **Done** | `mysql_list_tables` (12 tables, `snapshot-hub@replica`), `mysql_describe_table` (proposals/votes — names only, no rows) |
| `oauth-gated-and-misc.md` | Cloudflare / Calendly / Docusign / Slash / Browserbase | **Done (schema-only)** | Slash reachable (`list_endpoints`, 100+ endpoints — financial/PII, not exercised); Cloudflare/Calendly/Docusign OAuth-gated (auth not started); Browserbase tools listed, no sessions opened |

Also: `../built-in-tools.md` — non-MCP harness tool + Skill catalog (Agent/Bash/Read/Write/Edit/ToolSearch/Skill/Monitor/WebSearch/WebFetch + Skill list). **Done.**

## Status: COMPLETE (ongoing maintenance)
Per-MCP map done; structure/refactor pass done (2026-06-27) — `INDEX.md` links mcp/ + channels/ + built-in-tools.md, contacts/ ↔ channels/ cross-linked, `../mcp-servers.md` demoted to a pointer.

Only remaining optional item: Google Workspace de-stub — `list_labels` / `list_calendars` / `list_recent_files` (structure only) to lift `google-workspace.md` off stub. Otherwise re-verify drifted facts rather than net-new mapping.
