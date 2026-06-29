# Socials & external tooling

How Tony reaches people, and the external/social tools in play. Mark anything not directly confirmed **(unverified)**.

## Reach (where I talk to the team)
All via the **Metro** comms daemon — see `accounts-and-stations.md`.
- **Discord** — Snapshot Labs guild **`955773041898573854`**. Posting via the "Metro" bot account (`d0`).
- **Telegram** — bots `t0`/`t1`/`t2` + the `telegram-user` station.
- **XMTP** — production wallet-to-wallet messaging (`x0`/`x1`/`tony`).

## External / social tooling
### X / Twitter
- Reachable via **Zapier** (X actions) or the **X API** directly.
- Use the Zapier flow (`list_enabled_zapier_actions` → discover → enable → execute) when posting/reading on X.
- **A dedicated "x" MCP exists in `~/.claude.json` config but is UNREACHABLE** (static HTTP `http://127.0.0.1:8000/mcp`, no launcher, nothing on :8000) → the BOOTSTRAP "X auto-like queued @SnapshotLabs tweets" task is BLOCKED. See `knowledge-base/mcp/oauth-gated-and-misc.md`. Use the Zapier/X-API path instead.

### Typefully (content pipeline)
- Used by **Amalio** for content/marketing. Flow: **JARVIS (approval) → Typefully (intermediate/scheduling) → analytics.**
- Analytics is a Typefully **pro** feature (X API usable for it).
- **Cap: 15 posts/month** on Typefully → pushing drafts via API isn't viable, so Amalio posts from the dashboard instead. (Low-priority context, not a Tony task.) See `contacts/amalio.md`.

### JARVIS
- Team tool for **content approval** (the first step of Amalio's content pipeline). **(Details beyond "content approval gate" unverified.)**

## Notes
- X/Typefully/JARVIS specifics come from the gtm-channel context with Amalio (confirmed 2026-06-26); treat the pipeline as accurate but the internal mechanics of JARVIS as **(unverified)**.
