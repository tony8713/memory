# PROGRESS.md — knowledge-base self-improvement tracker

The single source of truth for what's mapped, what's stale, and what to do next. Each hourly tick reads this first (PART 3 of LOOP.md), advances one meaningful step, then updates this file. Keep it honest and high-signal.

Legend: **Mapped** = done & verified · **Partial** = started, needs deepening/verification · **TODO** = not started.

## Mapped
- `knowledge-base/mcp-servers.md` — seed catalog of connected MCP servers + key tools + caveats (server-level overview; not yet tool-by-tool).
- `knowledge-base/accounts-and-stations.md` — comms identities (XMTP/Discord/Telegram) + stations + per-station capabilities (public IDs only).
- `knowledge-base/repos-and-infra.md` — repos, local paths, relayer/monitoring/deploy infra.
- `knowledge-base/socials-and-external.md` — reach + X/Typefully/JARVIS.
- Top-level structure: `README.md`, `INDEX.md`, `STATUS.md`, `reports/`, `contacts/`, `skills/`, `preferences/`, `memory/`.
- `knowledge-base/mcp/` — per-MCP tool-by-tool files STARTED (2026-06-27): `metro.md`, `snapshot.md`, `better-stack.md` **Done** (real read-only calls captured), index in `mcp/README.md`. See Partial for findings + remaining servers.

## Partial
- `knowledge-base/mcp-servers.md` is a **server-level** overview only. The deeper **tool-by-tool** capability map (`knowledge-base/mcp/`, one file per server) is not built yet — see TODO.
- **`knowledge-base/mcp/`** — 3 of ~16 servers done (`metro`, `snapshot`, `better-stack`). Real findings captured: metro accounts (xmtp x0 `0xF7Bd…d26E`/x1 `0x9ec2…CF70`, discord d0 Metro `1502253402384633997`, telegram t0/t1/t2 + telegram-user) with per-station verbs; Snapshot whoami addr `0x257c61Ba…E88Ef` / alias `0x71e1C118…8146` (authorized); Better Stack mana source 1474369 + SX Indexers dashboard 1041825 confirmed. Remaining ~13 servers TODO (see mcp/README.md table).
- **`knowledge-base/channels/`** — STARTED (2026-06-27). `channels/README.md` index created from session context (Discord guild 955773041898573854 + 7 lines, Telegram t0/telegram-user, XMTP Less line) with a participant ID reference cross-linked to contacts/. Several Discord channel purposes/rosters still **unverified** — needs live `group_info`/`read`/`list_accounts` confirmation, then optional per-channel files.

## TODO
- **`knowledge-base/mcp/` (per-MCP tool-by-tool)** — 3 done (metro/snapshot/better-stack), remaining:
  - Zapier (`list_enabled_zapier_actions`), Sentry (`find_organizations`/`find_projects`), Tenderly (`list_projects`) — TODO (next batch)
  - Notion (`notion-get-users`/`notion-search`), Gmail (`list_labels`), Google Calendar (`list_calendars`)/Drive (`list_recent_files`), Snapshot_MySQL (`mysql_list_tables`) — TODO
  - Then: Fireflies, Intercom, Netlify, Cloudflare, Slash, Browserbase, others — TODO
- **Cross-linking** — once channels/ and mcp/ exist, link them from INDEX.md and the kb README, and cross-reference contacts/ ↔ channels/.

## Next focus (for the next tick)
Continue per-MCP `knowledge-base/mcp/` with the NEXT batch via SAFE read-only calls only: **Zapier** (`list_enabled_zapier_actions`), **Sentry** (`find_organizations`/`find_projects`), **Tenderly** (`list_projects`), then **Notion** (`notion-get-users`/`notion-search`), **Gmail** (`list_labels`), **Google** Calendar/Drive read-onlys. Mark write/mutating tools documented-not-tested. Also: use metro `group_info`/`read` to VERIFY the still-unverified Discord channel rosters/purposes in `channels/README.md` (not yet done).
