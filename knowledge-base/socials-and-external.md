# Socials & external tooling

How Tony reaches people, and the external/social tools in play. Mark anything not directly confirmed **(unverified)**.

## Reach (where I talk to the team)
All via the **Metro** comms daemon — see `accounts-and-stations.md`.
- **Discord** — Snapshot Labs guild **`955773041898573854`**. Posting via the "Metro" bot account (`d0`).
- **Telegram** — bots `t0`/`t1`/`t2` + the `telegram-user` station.
- **XMTP** — production wallet-to-wallet messaging (`x0`/`x1`/`tony`).

## External / social tooling
### X / Twitter
- **X-POSTING IS CURRENTLY UNAVAILABLE (confirmed 2026-06-30).** When Less asked the bot to "tweet something," there was **no live posting path**: the dedicated **"x" MCP is down** (static HTTP `http://127.0.0.1:8000/mcp` in `~/.claude.json`, no launcher, nothing on :8000), and **0 Zapier X actions are enabled** (`list_enabled_zapier_actions` empty), so neither the MCP nor the Zapier route can post as-is.
- **Enablement paths offered to Less (awaiting his choice):** (1) bring up the `x` MCP (start a listener on :8000); (2) connect X via **Zapier** OAuth (`discover_zapier_actions` → `enable_zapier_action` → execute write); (3) **Typefully** (but it caps 15 posts/month — see below). Until one is chosen, do NOT promise a tweet went out.
- (Reading/auto-like was always blocked for the same "x" MCP-down reason — the BOOTSTRAP "X auto-like queued @SnapshotLabs tweets" task stays BLOCKED.) See `knowledge-base/mcp/oauth-gated-and-misc.md`.

### Typefully (content pipeline)
- Used by **Amalio** for content/marketing. Flow: **JARVIS (approval) → Typefully (intermediate/scheduling) → analytics.**
- Analytics is a Typefully **pro** feature (X API usable for it).
- **Cap: 15 posts/month** on Typefully → pushing drafts via API isn't viable, so Amalio posts from the dashboard instead. (Low-priority context, not a Tony task.) See `contacts/amalio.md`.

### JARVIS
- Team tool for **content approval** (the first step of Amalio's content pipeline). **(Details beyond "content approval gate" unverified.)**

## Notes
- X/Typefully/JARVIS specifics come from the gtm-channel context with Amalio (confirmed 2026-06-26); treat the pipeline as accurate but the internal mechanics of JARVIS as **(unverified)**.
