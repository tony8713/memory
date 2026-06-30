# Socials & external tooling

How Tony reaches people, and the external/social tools in play. Mark anything not directly confirmed **(unverified)**.

## Reach (where I talk to the team)
All via the **Metro** comms daemon — see `accounts-and-stations.md`.
- **Discord** — Snapshot Labs guild **`955773041898573854`**. Posting via the "Metro" bot account (`d0`).
- **Telegram** — bots `t0`/`t1`/`t2` + the `telegram-user` station.
- **XMTP** — production wallet-to-wallet messaging (`x0`/`x1`/`tony`).

## External / social tooling
### X / Twitter
- **X MCP status CORRECTED (2026-06-30, supersedes the earlier "x MCP down at :8000" note).** The old dead `x` entry (`http://127.0.0.1:8000/mcp`, no launcher) has been **REPLACED by a connected cloud entry `x-cloud` → `https://api.x.com/mcp`** (X's official hosted MCP, "xmcp", bearer-auth — token NOT recorded here, secret). `claude mcp list` shows it **✔ Connected** and reachable (`initialize` + `tools/list` work — **24 tools**).
- **BUT it is READ / BOOKMARK-ONLY.** Tools are: `search_posts` / `search_news` / `search_users`, get timelines / mentions / trends, `get_users_me`, and **create/delete *bookmarks*** — there is **NO `create_post`/tweet tool**. So **X-POSTING IS STILL UNAVAILABLE**, now for a different reason: the token's access tier grants **read + bookmark only, no publish**.
- **Session caveat:** the `x-cloud` read tools aren't loaded in the current loop session (the entry was added after session start) — needs a **reconnect / `/mcp` refresh** to surface the read tools.
- **To enable posting (awaiting Less's choice):** (1) a **write-capable X token tier** (exposes a create-post tool on `x-cloud`); (2) **Zapier X write** via OAuth (`discover_zapier_actions` → `enable_zapier_action` → execute write); (3) **Typefully** (caps 15 posts/month — see below). Until one is chosen, do NOT promise a tweet went out.
- (Reading/auto-like: the BOOTSTRAP "X auto-like queued @SnapshotLabs tweets" task is now *technically* unblockable on the read side via `x-cloud` once the session reconnects, but there is no like/auto-like tool exposed — only bookmark — so treat the auto-like step as still not directly serviceable.) See `knowledge-base/mcp/oauth-gated-and-misc.md`.

### Typefully (content pipeline)
- Used by **Amalio** for content/marketing. Flow: **JARVIS (approval) → Typefully (intermediate/scheduling) → analytics.**
- Analytics is a Typefully **pro** feature (X API usable for it).
- **Cap: 15 posts/month** on Typefully → pushing drafts via API isn't viable, so Amalio posts from the dashboard instead. (Low-priority context, not a Tony task.) See `contacts/amalio.md`.

### JARVIS
- Team tool for **content approval** (the first step of Amalio's content pipeline). **(Details beyond "content approval gate" unverified.)**

## Notes
- X/Typefully/JARVIS specifics come from the gtm-channel context with Amalio (confirmed 2026-06-26); treat the pipeline as accurate but the internal mechanics of JARVIS as **(unverified)**.
