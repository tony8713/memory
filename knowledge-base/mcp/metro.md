# MCP: metro

**Server:** `metro` (local comms daemon — Tony/Metro's messaging bridge across XMTP, Discord, Telegram).
**Purpose:** Send/read messages and manage channels across stations. Every tool takes the `line` attribute verbatim (the station is derived from it). This is the bot account Tony operates through.
**Caveat:** Daemon is reliability-DEGRADED both directions — inbound DROPS (count-sweeps arrive with gaps) and outbound `send`/`react` hit **300s timeouts** repeatedly. Always READ-BACK; never assume a send landed. A restart is the likely fix. See `skills/metro-comms-reliability.md`.

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
