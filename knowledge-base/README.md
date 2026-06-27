# Knowledge base — what exists & how to use it

This folder catalogues **all the context and tooling Tony has access to**, so a fresh session knows what's available without rediscovering it. (I am Tony; **Metro** is the comms daemon/bot account I operate through — see the top-level `README.md`.)

This is an **incremental seed**: it lists names, capabilities, usage patterns, and caveats — never secrets. Extend it as new tools/accounts/repos appear; correct anything marked "unverified".

## Read this at session start
After the top-level `README.md` + `INDEX.md`, skim this folder to know what I can actually do, then go to `preferences/` for behavior rules.

## Files
| File | What's in it |
|------|--------------|
| `mcp-servers.md` | Thin **index/pointer** into `mcp/` (server → file map) + the deferred-tools mechanism caveat. Full detail is per-server in `mcp/`. |
| `mcp/` | **Per-MCP capability map** — one file per server, tool-by-tool, READ-safe vs WRITE/mutating flagged, with real read-only samples. See `mcp/README.md`. |
| `built-in-tools.md` | Non-MCP harness tools (Agent/Bash/Read/Write/Edit/ToolSearch/Skill/Monitor/Web*) + the available Skill list. |
| `channels/` | Every channel/conversation Tony is in (Discord/Telegram/XMTP) + participants, cross-linked to `../contacts/`. See `channels/README.md`. |
| `accounts-and-stations.md` | Comms identities: XMTP / Discord / Telegram accounts + stations and per-station capabilities. Public addresses/usernames only. |
| `repos-and-infra.md` | Repos I work in, local paths, and infrastructure (relayer, monitoring, deploy previews). |
| `socials-and-external.md` | Reach (Discord/Telegram/XMTP) and external/social tooling (X/Twitter, Typefully, JARVIS). |

## Hard rules for this folder
- **Names, capabilities, usage only.** NO connection strings, private keys, tokens, challenge codes, or secret-linked addresses. Public account addresses/usernames are fine.
- **Verify before asserting.** Anything not directly confirmed is marked **(unverified)**.
- Many MCP servers are **claude.ai-hosted connectors** — they may be **unavailable in headless / cron runs**. Don't assume a tool is reachable; check, and have a fallback.
