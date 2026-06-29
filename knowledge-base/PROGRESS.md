# PROGRESS.md — knowledge-base self-improvement tracker

The single source of truth for what's mapped, what's stale, and what to do next. Each hourly tick reads this first (PART 3 of LOOP.md), advances one meaningful step, then updates this file. Keep it honest and high-signal.

Legend: **Mapped** = done & verified · **Partial** = started, needs deepening/verification · **TODO** = not started.

## Mapped
- `knowledge-base/mcp-servers.md` — now a thin **index/pointer** into `mcp/` (server → file map + deferred-tools caveat); full detail lives in the per-server files.
- `knowledge-base/accounts-and-stations.md` — comms identities (XMTP/Discord/Telegram) + stations + per-station capabilities (public IDs only).
- `knowledge-base/repos-and-infra.md` — repos, local paths, relayer/monitoring/deploy infra.
- `knowledge-base/socials-and-external.md` — reach + X/Typefully/JARVIS.
- Top-level structure: `README.md`, `INDEX.md`, `STATUS.md`, `reports/`, `contacts/`, `skills/`, `preferences/`, `memory/`.
- `knowledge-base/mcp/` — per-MCP tool-by-tool files: `metro.md`, `snapshot.md`, `better-stack.md`, `zapier.md`, `sentry.md`, `tenderly.md`, `notion.md`, `intercom.md`, `netlify.md`, `fireflies.md`, `snapshot-mysql.md` **Done** (real read-only calls captured); `oauth-gated-and-misc.md` (Cloudflare/Calendly/Docusign/Slash/Browserbase) **Done (schema-only)**; `google-workspace.md` **Done (2026-06-28, structure-only)**. Index in `mcp/README.md`. Per-MCP map complete.
- `knowledge-base/built-in-tools.md` **Done (2026-06-27)** — non-MCP harness tool catalog (Agent + sub-agent types, Bash/Read/Write/Edit, ToolSearch, Skill, Monitor/WebSearch/WebFetch, SendMessage/TaskStop/Worktree) + the available Skill list.
- `mcp/snapshot-mysql.md` (2026-06-27) — Snapshot_MySQL REACHABLE: read-only `snapshot-hub@replica`, 12 tables; proposals (34c) + votes (15c, PK voter+space+proposal) schema captured (names only, NO rows). Distinct from the mana/PlanetScale relayer DB.
- `mcp/oauth-gated-and-misc.md` (2026-06-27) — **Slash** REACHABLE (banking/spend API, 100+ endpoints, financial PII → only `GET /tokens/prices` safe-public, not exercised); **Cloudflare/Calendly/Docusign** OAuth-gated (auth NOT started, verify in main); **Browserbase** tools listed but no session opened.
- `knowledge-base/channels/` Discord rosters — **ALL known Discord lines verified 2026-06-27** via metro `read`: SX Ledger thread `1517956186929102869`, #api `1030772407226601522`, interns `1478881436168880218`, plus the last two `1125390177888645231` (team/payment-feed: Less+Amalio+Metro, snapshot/Schnaps bots) and `1508453453930692669` (1:1 DM tonyshot26↔Metro, setup/onboarding). No `(unverified)` Discord rows remain.
- **Per-MCP capability map — COMPLETE (2026-06-27)**, now ongoing-maintenance. All reachable servers mapped tool-by-tool in `mcp/`; only `google-workspace.md` remains a deliberate (privacy) stub.
- **STRUCTURE/REFACTOR pass — DONE (2026-06-27, post-1010).** `INDEX.md` now links EVERYTHING (README/LOOP/STATUS/PROGRESS, all top-level KB files, `mcp/` + every per-server file, `channels/`, `built-in-tools.md`, all skills/preferences/memory files, contacts/README + each contact, reports/). `mcp-servers.md` demoted to a thin **index/pointer** into `mcp/` (de-duplicated; no info lost). `knowledge-base/README.md` Files table extended (mcp/, built-in-tools.md, channels/). Contacts ↔ channels cross-linked **both ways** (each trusted-5 contact got a "Key channels" block; `channels/README.md` roster already points to contacts/ files).

> Key per-server findings (Notion live teamspace `General` + Fabien/bots; Intercom workspace `n9dmxtcs`/59 articles; Netlify 3 teams incl. stagelabs→stage.box + snapshotlabs→11 SX sites; Sentry org `snapshot-labs`/16 projects/NO mana; Tenderly default project + ~100 chains; Zapier none-enabled; Snapshot_MySQL `snapshot-hub@replica`/12 tables) live in the respective `mcp/*.md` files — no longer duplicated here.

## Partial / open
- **`knowledge-base/channels/`** — index + **all known Discord lines verified (2026-06-27)**; cross-linked to contacts/. No unverified Discord rows remain. Optional next: split high-traffic channels into per-channel files.
- **`knowledge-base/mcp/google-workspace.md`** — **DE-STUBBED 2026-06-28** (structure-only). Drive left schema-only by choice (file names sensitive). Nothing further needed.

## TODO
- Nothing net-new outstanding. (All Discord lines now verified.) Optionally de-stub Google Workspace, then operate in maintenance mode (re-verify drifted facts; keep STATUS/reports fresh).

## Next focus (for the next tick)
This tick (2026-06-29, post-1830) **completed** the configured-vs-live MCP reconciliation: audited `~/.claude.json`, found the only configured-but-unreachable static-HTTP endpoints are **`x`** (top-level) and **`unreal-mcp`** (project `/`) — **both** point at the same dead local `127.0.0.1:8000/mcp` (no listener). Project-scoped `chrome-devtools-mcp` and `metro-http` are inactive in the current project scope. All rich servers come from the **claude.ai connector layer** (incl. new **NickAI**, OAuth-style, not exercised), NOT local config. Reconciliation table written into `mcp/README.md`. Also added skill `skills/xmtp-multi-client-installation-cap.md` (XMTP send-outage failure mode + fix sequence: dedup local client → revoke stale installations → ensure raw-privateKey loader patch is DEPLOYED + set `DERIVE_COUNT=1`).

**XMTP send-path recovery — DONE (2026-06-29 ~19:30 UTC).** The Fly deploy shipped the raw-privateKey loader patch + `DERIVE_COUNT=1`; the cloud `tony` client booted cleanly (synced 15 conversations, no rate-limit) and a real send to Less succeeded end-to-end. STATUS XMTP item flipped to RECOVERED; loader patch now committed/deployed (no deploy-drift). Closes the prior PART 4 next-focus.

**Concrete next focus (for the next tick):** **Watch for Less's #2185 rebase decision** — the sx-monorepo #2185 backend commits are superseded by #2181 on master; plan is to cherry-pick ONLY the UI commit `66db2dfd` once Less confirms (still BLOCKED awaiting Less). When Less decides, execute the cherry-pick and report. Secondary: track #2125 backend-only merge → then retarget UI PR #2195 to master; re-verify drifted IDs/dashboards/sources; keep STATUS/reports fresh. KB capability map stays in maintenance mode (no net-new mapping outstanding).

**Optional cleanup (known recurring-conflict risk, NOT yet actioned):** consider stopping the competing local `box.metro.dispatcher` duplicate daemon. A second dispatcher racing the primary is a recurring source of inbound/MCP-stream conflicts (cf. the 2026-06-29 post-18:33 zombie-stream outage). Low-priority housekeeping — flag/confirm before killing; not blocking.

**Durable learning captured this tick (quiet, 2026-06-29 ~20:05 UTC):** metro write-verb 300s timeouts are "probably delivered, not failed" — always `read`-back before retrying to avoid double/triple-posting. Written into `skills/metro-comms-reliability.md` (failure mode 1) + `knowledge-base/mcp/metro.md` (write-verb timeout semantics).
