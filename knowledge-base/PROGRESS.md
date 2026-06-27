# PROGRESS.md — knowledge-base self-improvement tracker

The single source of truth for what's mapped, what's stale, and what to do next. Each hourly tick reads this first (PART 3 of LOOP.md), advances one meaningful step, then updates this file. Keep it honest and high-signal.

Legend: **Mapped** = done & verified · **Partial** = started, needs deepening/verification · **TODO** = not started.

## Mapped
- `knowledge-base/mcp-servers.md` — seed catalog of connected MCP servers + key tools + caveats (server-level overview; not yet tool-by-tool).
- `knowledge-base/accounts-and-stations.md` — comms identities (XMTP/Discord/Telegram) + stations + per-station capabilities (public IDs only).
- `knowledge-base/repos-and-infra.md` — repos, local paths, relayer/monitoring/deploy infra.
- `knowledge-base/socials-and-external.md` — reach + X/Typefully/JARVIS.
- Top-level structure: `README.md`, `INDEX.md`, `STATUS.md`, `reports/`, `contacts/`, `skills/`, `preferences/`, `memory/`.
- `knowledge-base/mcp/` — per-MCP tool-by-tool files: `metro.md`, `snapshot.md`, `better-stack.md`, `zapier.md`, `sentry.md`, `tenderly.md`, `notion.md`, `intercom.md`, `netlify.md`, `fireflies.md`, `snapshot-mysql.md` **Done** (real read-only calls captured); `oauth-gated-and-misc.md` (Cloudflare/Calendly/Docusign/Slash/Browserbase) **Done (schema-only)**; `google-workspace.md` **Stub**. Index in `mcp/README.md`. Per-MCP map effectively complete.
- `knowledge-base/built-in-tools.md` **Done (2026-06-27)** — non-MCP harness tool catalog (Agent + sub-agent types, Bash/Read/Write/Edit, ToolSearch, Skill, Monitor/WebSearch/WebFetch, SendMessage/TaskStop/Worktree) + the available Skill list.
- `mcp/snapshot-mysql.md` (2026-06-27) — Snapshot_MySQL REACHABLE: read-only `snapshot-hub@replica`, 12 tables; proposals (34c) + votes (15c, PK voter+space+proposal) schema captured (names only, NO rows). Distinct from the mana/PlanetScale relayer DB.
- `mcp/oauth-gated-and-misc.md` (2026-06-27) — **Slash** REACHABLE (banking/spend API, 100+ endpoints, financial PII → only `GET /tokens/prices` safe-public, not exercised); **Cloudflare/Calendly/Docusign** OAuth-gated (auth NOT started, verify in main); **Browserbase** tools listed but no session opened.
- `knowledge-base/channels/` Discord rosters — SX Ledger thread `1517956186929102869`, #api `1030772407226601522`, interns `1478881436168880218` **verified 2026-06-27** via metro `read` (handles/ids confirmed; "unverified" removed in `channels/README.md`).

## Partial
- `knowledge-base/mcp-servers.md` is a **server-level** overview only. The deeper **tool-by-tool** capability map (`knowledge-base/mcp/`, one file per server) is not built yet — see TODO.
- **`knowledge-base/mcp/`** — 10 done + 1 stub of ~16 servers. Batch-3 findings (2026-06-27): **Notion** — live teamspace is `General`; topical spaces (Finance/P&E/Fireflies/Home/Welcome) are archived; 6 people incl. **Fabien** (fabien@snapshot.org, not in trusted-5) + the 5 trusted; bots incl. Fireflies/Zapier/Tally/GrantDetectorBot wired in; create/update/move are WRITE. **Intercom** — workspace `n9dmxtcs`, Help Center help.snapshot.box, **59 published articles**; conversations/contacts carry PII (schema-only); `create_article`/`update_article` WRITE. **Netlify** — Tony in 3 teams: tony8713 (Owner), **stagelabs** (Developer → stage.box), **snapshotlabs** (Reviewer → snapshot.box + 10 more SX sites); full domain→project map captured; all updater services WRITE. **Fireflies** — Tony is org **admin**, 0 minutes/no meetings on this acct; heavy PII surface so schema-only; share/move/privacy/title WRITE. Batch-2 findings (2026-06-27): **Zapier** — `list_enabled_zapier_actions` returns NONE enabled (connected but blank; fronts 9000+ apps incl. Typefully; enable/execute_write are WRITE). **Sentry** — org `snapshot-labs` (regionUrl `https://us.sentry.io`), 16 projects (blockfinder, brovider, capsule, envelop, envelop-ui, keycard, laser, pineapple, score-api, snapshot, snapshot-hub, snapshot-relayer, snapshot-sequencer, snapshot-sidekick, snapshot-webhook, stamp) — **NO mana project**; `execute_sentry_tool` is WRITE. **Tenderly** — 1 project ("Project", default; not a curated SX workspace), ~100 networks (all SX chains present); simulate/trace/vnet-create/fund/set-balance/send all WRITE/mutating. Earlier batch-1 findings (metro/snapshot/better-stack) unchanged. Remaining ~9 servers TODO.
- **`knowledge-base/channels/`** — index built; 3 key Discord lines (SX Ledger thread, #api, interns) **verified 2026-06-27** via metro `read`. Still **unverified**: lines `1125390177888645231` and `1508453453930692669` (purpose+roster) — confirm next, then optional per-channel files.

## TODO
- **`knowledge-base/mcp/` (per-MCP tool-by-tool)** — 10 done (metro/snapshot/better-stack/zapier/sentry/tenderly/notion/intercom/netlify/fireflies) + google-workspace stub, remaining:
  - Google Workspace read-onlys (`list_labels`/`list_calendars`/`list_recent_files`, structure only when warranted) — TODO (stub exists)
  - Snapshot_MySQL (`mysql_list_tables`/`mysql_describe_table`/`mysql_read_query`) — TODO
  - Then: Cloudflare, Slash, Browserbase, Docusign, Calendly, others — TODO
  - Built-in/deferred harness tool catalog (non-MCP) — TODO
- **Cross-linking** — once channels/ and mcp/ exist, link them from INDEX.md and the kb README, and cross-reference contacts/ ↔ channels/.

## Next focus (for the next tick)
**STRUCTURE/REFACTOR pass** (per-MCP map + built-in catalog now effectively complete):
1. Ensure `INDEX.md` covers everything new — `knowledge-base/mcp/` (incl. snapshot-mysql + oauth-gated-and-misc), `knowledge-base/built-in-tools.md`, and `knowledge-base/channels/`. Add links if missing.
2. Cross-link contacts/ ↔ channels/ (each trusted-5 contact → the channels they appear in; each channel roster → contact files).
3. Prune/merge any stale or duplicate notes (e.g. `mcp-servers.md` server-level overview vs the now-complete per-MCP `mcp/` files — consider demoting `mcp-servers.md` to a pure pointer).
4. Optional remaining read-only: Google Workspace `list_labels`/`list_calendars`/`list_recent_files` (structure only) to lift `google-workspace.md` off stub; VERIFY Discord lines `1125390177888645231` + `1508453453930692669` via metro `read`.
Then move to ongoing maintenance/verification mode (re-verify drifted facts rather than net-new mapping).
