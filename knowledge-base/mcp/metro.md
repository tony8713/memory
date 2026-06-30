# MCP: metro

**Server:** `metro` (local comms daemon — Tony/Metro's messaging bridge across XMTP, Discord, Telegram).
**Purpose:** Send/read messages and manage channels across stations. Every tool takes the `line` attribute verbatim (the station is derived from it). This is the bot account Tony operates through.
## Transport architecture + the stream-wedge fix (2026-06-30) — SUPERSEDES the old "read-verb degradation" notes
The recurring **`read` 300s timeouts / zombie SSE stream / post-deploy MCP flakiness** were a **server bug, now FIXED at the source** (NOT a passive "restart / `/mcp` reconnect" workaround anymore).

- **File:** `apps/mcp/src/mcp/index.ts` (metro-protocol).
- **Old (broken) design:** a **single GLOBAL** `StreamableHTTPServerTransport` that **`rebind()`'d** (close + mint a new session id) on **every** session-id mismatch. Failure modes:
  1. Rebind tore down **in-flight reads** → the 300s `read` hangs.
  2. Rebind **orphaned the inbound SSE stream** (zombie stream).
  3. With a **2nd concurrent client**, the two clients entered **permanent mutual-rebind thrash** (each request stole/closed the other's transport).
  4. A **deploy bounce** (e.g. 18:33Z) kicked off the reconnect storm that wedged the orchestrator's stream.
- **New (fixed) design — commit `04f6b6f`:** **per-session transports** — `Map<sessionId, {server, transport}>`. Create a transport **only on `initialize`**; route every later request by `mcp-session-id`; return **404** on an unknown/stale id and **never close or steal a live transport**. Removed the `rebind` / `syncSession` / `_webStandardTransport` hack. Inbound relay + 25s keepalive **broadcast per-session**. Verified: build clean, **137 tests pass**, deployed to Fly app `metro`, live-checked (per-session works; bogus id → 404 without disturbing the live session; no rebind churn; reads return instantly).
- **Companion mitigation:** the local `box.metro.dispatcher` daemon was **RETIRED** (`launchctl bootout`) so there is **one client per inbox** — removing the multi-client trigger for thrash #3.
- **Caveat (deploy-drift, open):** `04f6b6f` + `2f9a5fa` (the latter also carries the `packages/xmtp` raw-privateKey loader patch) are on **LOCAL main only, NOT pushed to origin** (branch behind origin via unrelated telegram-user commits). Cloud runs the fix (deploys from working tree); origin still needs reconcile + push.

**Write-verb timeout semantics (still applies):** even with the fix, treat any `send`/`reply`/`react` MCP idle-timeout ERROR as **"unknown, probably delivered" — NOT "failed."** After a write timeout, FIRST `read` the channel to confirm it landed; do NOT blindly retry or you double/triple-post (spam). Confirmed 2026-06-29: three replies all "timed out" yet a `read` showed all three posted. See `skills/metro-comms-reliability.md`.

## Real captured context (`mcp__metro__list_accounts`, 2026-06-27)
Accounts (PUBLIC identity only — no tokens/keys/mnemonic ever returned):

- **xmtp** (env `production`):
  - `x0` — `0xF7Bd80f4321168809Bb44eed1531DBCf2477d26E` (inboxId `577979ef…d8828`, keySource `derive:0`)
  - `x1` — `0x9ec26A6971064F3109133cBCc1CaD684b8E4CF70` (inboxId `6d307513…a6f8f`, keySource `derive:1`)
- **discord**:
  - `d0` — userId `1502253402384633997`, username **Metro**, `ready: true`
- **telegram** (bots):
  - `t0` — botId `8798949149`, `@metro30593bot`
  - `t1` — botId `8350110003`, `@bobmetrobot`
  - `t2` — botId `8767266820`, `@alicemetrobot`
- **telegram-user**:
  - `default` (user-account session)

### Capabilities per station (verbs the daemon honors)
- **xmtp:** react, read, reply, send, unreact (NO edit/delete)
- **telegram (bots):** delete, edit, react, reply, send, unreact (NO read — bots can't read history)
- **telegram-user:** delete, edit, react, read, reply, send, unreact (full)
- **discord:** delete, edit, react, reply, send, unreact, read (full)

## Tool inventory
READ-safe (used freely in the loop):
- `list_accounts` — list configured accounts + capabilities across stations. **READ.** No args. (called above)
- `read` — recent message history for a `line`. **READ.** Primary inbox-sweep tool. (subject to 300s transport drops)
- `group_info` — metadata/roster for a group line. **READ.** Use to verify channel rosters.

WRITE / mutating (documented from schema — invoke only for real comms work, never to "test"):
- `send` — post text and/or media (`attachments`, optional `reply_to`) to a `line`. **WRITE.**
- `reply` — quote a `message_id` with `text`. **WRITE.**
- `react` / `unreact` — add/remove an emoji on a `message_id`. **WRITE.**
- `edit` — edit a `message_id` (where station supports it). **WRITE.**
- `delete` — delete a `message_id` (where station supports it). **WRITE/destructive.**
- `dm` — open/send a direct message. **WRITE.**
- `ask` — prompt/ask flow. **WRITE.**
- `create_channel` — create a new channel. **WRITE.**
- `close_channel` — close a channel. **WRITE/destructive.**
- `set_channel_metadata` — set channel name/description/metadata. **WRITE.**
- `add_members` — add members to a channel. **WRITE.**
- `remove_members` — remove members from a channel. **WRITE/destructive.**

## Notes
- Inbound messages arrive as `<channel source="metro" line="…" from="…" station="…" message_id="…">`. Respond by passing `line` verbatim.
- Inbound attachments surface as a note with an absolute `local_path` — `Read` that path to view.
- Tool-approval prompts are relayed to the same chat — answer "yes <id>" / "no <id>".
- Auth/availability: available in this headless worker (list_accounts succeeded). No secrets exposed.
