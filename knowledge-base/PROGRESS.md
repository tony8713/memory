# PROGRESS.md — knowledge-base self-improvement tracker

The single source of truth for what's mapped, what's stale, and what to do next. Each hourly tick reads this first (PART 3 of LOOP.md), advances one meaningful step, then updates this file. Keep it honest and high-signal.

Legend: **Mapped** = done & verified · **Partial** = started, needs deepening/verification · **TODO** = not started.

## Mapped
- `knowledge-base/mcp-servers.md` — seed catalog of connected MCP servers + key tools + caveats (server-level overview; not yet tool-by-tool).
- `knowledge-base/accounts-and-stations.md` — comms identities (XMTP/Discord/Telegram) + stations + per-station capabilities (public IDs only).
- `knowledge-base/repos-and-infra.md` — repos, local paths, relayer/monitoring/deploy infra.
- `knowledge-base/socials-and-external.md` — reach + X/Typefully/JARVIS.
- Top-level structure: `README.md`, `INDEX.md`, `STATUS.md`, `reports/`, `contacts/`, `skills/`, `preferences/`, `memory/`.

## Partial
- `knowledge-base/mcp-servers.md` is a **server-level** overview only. The deeper **tool-by-tool** capability map (`knowledge-base/mcp/`, one file per server) is not built yet — see TODO.
- **`knowledge-base/channels/`** — STARTED (2026-06-27). `channels/README.md` index created from session context (Discord guild 955773041898573854 + 7 lines, Telegram t0/telegram-user, XMTP Less line) with a participant ID reference cross-linked to contacts/. Several Discord channel purposes/rosters still **unverified** — needs live `group_info`/`read`/`list_accounts` confirmation, then optional per-channel files.

## TODO
- **`knowledge-base/mcp/` (per-MCP tool-by-tool)** — not created. One file per server, tool-by-tool: name, what it does, safe example, real output shape from a READ-ONLY call where safe, and explicit write/mutating flags (documented-not-tested). Suggested order over successive ticks:
  - metro (read/list_accounts/group_info — read-only) — TODO
  - Snapshot (snapshot-whoami, snapshot-query, snapshot-schema — read-only) — TODO
  - Better Stack (uptime_list_monitors, telemetry_list_sources/dashboards — read-only) — TODO
  - Then: Snapshot_MySQL, Sentry, Notion, Gmail, Google Calendar/Drive, Fireflies, Intercom, Netlify, Tenderly, Cloudflare, Slash, Zapier, Browserbase, others — TODO
- **Cross-linking** — once channels/ and mcp/ exist, link them from INDEX.md and the kb README, and cross-reference contacts/ ↔ channels/.

## Next focus (for the next tick)
Begin the per-MCP `knowledge-base/mcp/` files (one per server), starting with **metro** (`read`/`list_accounts`/`group_info` — read-only), **Snapshot** (`snapshot-whoami`, `snapshot-query`, `snapshot-schema` — read-only), and **Better Stack** (`uptime_list_monitors`, `telemetry_list_sources`, `telemetry_list_dashboards` — read-only), using SAFE read-only calls only to capture real output shape. Document write/mutating tools from their schemas without invoking them. Also use those `group_info`/`list_accounts` calls to VERIFY the unverified entries in `channels/README.md`.
