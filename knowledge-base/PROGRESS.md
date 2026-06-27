# PROGRESS.md — knowledge-base self-improvement tracker

The single source of truth for what's mapped, what's stale, and what to do next. Each hourly tick reads this first (PART 3 of LOOP.md), advances one meaningful step, then updates this file. Keep it honest and high-signal.

Legend: **Mapped** = done & verified · **Partial** = started, needs deepening/verification · **TODO** = not started.

## Mapped
- `knowledge-base/mcp-servers.md` — seed catalog of connected MCP servers + key tools + caveats (server-level overview; not yet tool-by-tool).
- `knowledge-base/accounts-and-stations.md` — comms identities (XMTP/Discord/Telegram) + stations + per-station capabilities (public IDs only).
- `knowledge-base/repos-and-infra.md` — repos, local paths, relayer/monitoring/deploy infra.
- `knowledge-base/socials-and-external.md` — reach + X/Typefully/JARVIS.
- Top-level structure: `README.md`, `INDEX.md`, `STATUS.md`, `reports/`, `contacts/`, `skills/`, `preferences/`, `memory/`.
- `knowledge-base/mcp/` — per-MCP tool-by-tool files: `metro.md`, `snapshot.md`, `better-stack.md`, `zapier.md`, `sentry.md`, `tenderly.md` **Done** (real read-only calls captured); `google-workspace.md` **Stub** (schema-only, no user data read). Index in `mcp/README.md`. See Partial for findings + remaining servers.

## Partial
- `knowledge-base/mcp-servers.md` is a **server-level** overview only. The deeper **tool-by-tool** capability map (`knowledge-base/mcp/`, one file per server) is not built yet — see TODO.
- **`knowledge-base/mcp/`** — 6 done + 1 stub of ~16 servers. Batch-2 findings (2026-06-27): **Zapier** — `list_enabled_zapier_actions` returns NONE enabled (connected but blank; fronts 9000+ apps incl. Typefully; enable/execute_write are WRITE). **Sentry** — org `snapshot-labs` (regionUrl `https://us.sentry.io`), 16 projects (blockfinder, brovider, capsule, envelop, envelop-ui, keycard, laser, pineapple, score-api, snapshot, snapshot-hub, snapshot-relayer, snapshot-sequencer, snapshot-sidekick, snapshot-webhook, stamp) — **NO mana project**; `execute_sentry_tool` is WRITE. **Tenderly** — 1 project ("Project", default; not a curated SX workspace), ~100 networks (all SX chains present); simulate/trace/vnet-create/fund/set-balance/send all WRITE/mutating. Earlier batch-1 findings (metro/snapshot/better-stack) unchanged. Remaining ~9 servers TODO.
- **`knowledge-base/channels/`** — STARTED (2026-06-27). `channels/README.md` index created from session context (Discord guild 955773041898573854 + 7 lines, Telegram t0/telegram-user, XMTP Less line) with a participant ID reference cross-linked to contacts/. Several Discord channel purposes/rosters still **unverified** — needs live `group_info`/`read`/`list_accounts` confirmation, then optional per-channel files.

## TODO
- **`knowledge-base/mcp/` (per-MCP tool-by-tool)** — 6 done (metro/snapshot/better-stack/zapier/sentry/tenderly) + google-workspace stub, remaining:
  - Notion (`notion-get-users`/`notion-search`) — TODO (next batch)
  - Google Workspace read-onlys (`list_labels`/`list_calendars`/`list_recent_files`, structure only when warranted) — TODO (stub exists)
  - Snapshot_MySQL (`mysql_list_tables`), Intercom, Netlify, Fireflies — TODO
  - Then: Cloudflare, Slash, Browserbase, Docusign, Calendly, others — TODO
- **Cross-linking** — once channels/ and mcp/ exist, link them from INDEX.md and the kb README, and cross-reference contacts/ ↔ channels/.

## Next focus (for the next tick)
Continue per-MCP `knowledge-base/mcp/` with the NEXT batch via SAFE read-only calls only: **Notion** (`notion-get-users`/`notion-search`), **Google Workspace** read-onlys (structure only — labels/calendar names/recent-file names, NEVER message/event/file bodies), then **Intercom** / **Netlify** / **Fireflies** read-only listings. Mark write/mutating tools documented-not-tested. THEN (carried over, still not done): use metro `group_info`/`read` to VERIFY the still-unverified Discord channel rosters/purposes in `channels/README.md`.
