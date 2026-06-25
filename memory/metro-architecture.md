# Metro inbound architecture

For Metro `<channel>` inbound to reach a Claude Code session, use a **locally-registered HTTP MCP pointing at the cloud endpoint**, NOT the claude.ai-managed connector (the managed connector does not surface `<channel>` inbound).

Setup:
```
claude mcp add --transport http --scope user metro "https://mcp.metro.box/mcp?token=<METRO_MCP_HTTP_TOKEN>"
claude mcp remove "claude.ai Metro"
# then restart the session
```
Auth is a **URL query param `?token=`**, NOT a Bearer header.

## Two Metro instances
- **Cloud**: Fly app `metro` (owner snack-labs, iad), serves `https://mcp.metro.box/mcp`. The session's inbound source.
- **Local**: launchd `box.metro.dispatcher`, MCP on `127.0.0.1:8420/mcp`. Local trains are competing duplicates that fight cloud over the same bot tokens (Telegram getUpdates Conflict, XMTP identity).

## Debug points when inbound stops
- **Allowlist**: `METRO_CHANNEL_ALLOWLIST` (comma-separated, no glob; literal `*` = all; default hash allows nobody). Code: `metro-protocol/apps/mcp/src/mcp/index.ts` ~L128-133. Drop log: `[metro-mcp] drop: sender not allowed`. Set on cloud: `fly secrets set METRO_CHANNEL_ALLOWLIST='*' -a metro`.
- **Trains are symlinks**: `~/.metro/trains/*.ts` → `~/work/metro-protocol/packages/<name>/src/index.ts` (monorepo restructure ~2026-06-24). Dangling symlinks are silently skipped (`train supervisor count:N` short).
- Cloud builds from the **local working tree** (Dockerfile `COPY . .`, not git) — uncommitted edits ship via `fly deploy -a metro`.
- Bounce local dispatcher: `launchctl kickstart -k gui/$(id -u)/box.metro.dispatcher`.

## Known regression pattern (2026-06-25 cloud cutover)
The Jun 25 cloud cutover re-symlinked live trains to `~/work/metro-protocol` but left `telegram-user.ts` as the old stub and stripped `TG_USER_*` creds from `~/.metro/.env`, so the telegram-user station stayed idle ("awaiting config"). Fix = restore the real train + creds. If a station is idle after a cutover, check the symlink target and the `.env` creds.
