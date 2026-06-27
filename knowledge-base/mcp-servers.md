# MCP servers — index / pointer

> **This file is now a thin index.** The full, tool-by-tool capability map (READ-safe vs WRITE/mutating, real read-only output samples, auth caveats) lives in **`mcp/`** — one file per server. Start there. See **[`mcp/README.md`](mcp/README.md)** for the status table.

**Caveat for all claude.ai-hosted connectors:** they may be **unavailable in headless / cron runs** — verify reachability, have a fallback. Most MCP tools are **deferred** (names loaded, schemas not): run **`ToolSearch`** with `select:<name>` (or a keyword query) to load a schema before calling, or it fails with InputValidationError. Deferred names appear in `<system-reminder>` messages.

## Server → file map

| Server | What it's for | File |
|---|---|---|
| **metro** | comms transport (the daemon/bot Tony operates through) — Discord/XMTP/Telegram send/read/react. Lossy: read-back is source of truth. | [`mcp/metro.md`](mcp/metro.md) |
| **Snapshot** | governance over GraphQL + signed votes/proposals (`$user` auto-bound; slug ids; unix-sec ts) | [`mcp/snapshot.md`](mcp/snapshot.md) |
| **Better Stack** | telemetry/logs/uptime; **mana logs source `1474369`/`mana_pino`**, **SX Indexers dashboard `1041825`** | [`mcp/better-stack.md`](mcp/better-stack.md) |
| **Zapier** | 9,000+ app bridge (Typefully etc.); none enabled yet | [`mcp/zapier.md`](mcp/zapier.md) |
| **Sentry** | error monitoring; org `snapshot-labs`, 16 projects, **NO mana project** | [`mcp/sentry.md`](mcp/sentry.md) |
| **Tenderly** | EVM simulation/tracing/vnets | [`mcp/tenderly.md`](mcp/tenderly.md) |
| **Notion** | docs/databases; `General` teamspace live | [`mcp/notion.md`](mcp/notion.md) |
| **Intercom** | conversations/contacts/help-center; workspace `n9dmxtcs` | [`mcp/intercom.md`](mcp/intercom.md) |
| **Netlify** | deploy/project/team services; Tony in 3 teams | [`mcp/netlify.md`](mcp/netlify.md) |
| **Fireflies** | meeting transcripts/summaries | [`mcp/fireflies.md`](mcp/fireflies.md) |
| **Snapshot_MySQL** | read-only SQL into `snapshot-hub@replica` (12 tables) | [`mcp/snapshot-mysql.md`](mcp/snapshot-mysql.md) |
| **Gmail / Calendar / Drive** | Google Workspace (read/draft/label) | [`mcp/google-workspace.md`](mcp/google-workspace.md) (stub) |
| **Cloudflare / Calendly / Docusign / Slash / Browserbase** | OAuth-gated + misc (Slash = banking/spend API) | [`mcp/oauth-gated-and-misc.md`](mcp/oauth-gated-and-misc.md) |

Non-MCP harness tools (Agent/Bash/Read/Write/Edit/ToolSearch/Skill/Monitor/WebSearch/WebFetch + the Skill list): **[`built-in-tools.md`](built-in-tools.md)**.

> The **mana relayer DB** is PostgreSQL on PlanetScale (psdb), accessed during #2186 diagnosis via a read-only grant — **not** via the Snapshot_MySQL tools. No credentials anywhere in this repo. See `repos-and-infra.md` + `../STATUS.md`.
