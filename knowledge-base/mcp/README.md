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

## Configured-vs-live reconciliation (audited 2026-06-29)
Two sources of MCP servers, **do not confuse them**:

1. **claude.ai account connectors** (Snapshot, Notion, Sentry, Tenderly, Netlify, Intercom, Fireflies, Zapier, Better Stack, Snapshot_MySQL, Gmail/Calendar/Drive, Cloudflare, Calendly, Docusign, Slash, Browserbase, NickAI, …). These are **NOT in `~/.claude.json`** — they come from the claude.ai integration layer and surface here as deferred tools. All the rich servers mapped in this folder are these. **NickAI** appears in the deferred-tool list now (OAuth-style `authenticate`/`complete_authentication` pair, not yet exercised).

2. **Locally-configured servers in `~/.claude.json`** — only a handful, and **most are project-scoped** (only active when cwd matches that project root). Audited:

| Name | Scope | Transport / target | Live here? | Note |
|---|---|---|---|---|
| `metro` | top-level + project `/` | http `mcp.metro.box` (token-in-URL) | **LIVE** | the comms daemon; token NOT recorded (secret) |
| `x-cloud` | top-level | http `https://api.x.com/mcp` (bearer-auth) | **LIVE (read-only)** | X's official hosted MCP "xmcp"; `claude mcp list` ✔ Connected, 24 tools. **READ/BOOKMARK-ONLY — no create_post/tweet** (token tier = read+bookmark). Replaces the old dead `x` :8000 entry (2026-06-30). Not loaded in current session (added post-start → needs reconnect). Token NOT recorded (secret). See `oauth-gated-and-misc.md` + `../socials-and-external.md`. |
| `unreal-mcp` | project `/` | http `127.0.0.1:8000/mcp` | **DEAD** | no launcher, nothing on :8000 — a local stub, unreachable. (The old `x` entry that shared this dead endpoint is gone, replaced by `x-cloud` above.) |
| `chrome-devtools-mcp` | project `/Users/tony` | stdio `npx chrome-devtools-mcp@latest` | inactive here | only loads when cwd = `/Users/tony`; not in current project scope |
| `metro-http` | project `/Users/tony/Cursor/bonustrack/metro/metro` | http `mcp.metro.box` (no token) | inactive here | duplicate metro endpoint scoped to the metro repo |

**Takeaway for future ticks (updated 2026-06-30):** `unreal-mcp` is the only remaining dead static-HTTP endpoint (`127.0.0.1:8000/mcp`, no listener) — do not chase it. The old dead `x` :8000 entry was **replaced by `x-cloud` (`https://api.x.com/mcp`, bearer-auth) — LIVE but read/bookmark-only, no posting** (see row above + `../socials-and-external.md`). Everything else genuinely available comes from the claude.ai connector layer (mapped in this folder) plus `metro` + `x-cloud`.
