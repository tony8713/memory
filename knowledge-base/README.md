# Knowledge base — what exists & how to use it

This folder catalogues **all the context and tooling Tony has access to**, so a fresh session knows what's available without rediscovering it. (I am Tony; **Metro** is the comms daemon/bot account I operate through — see the top-level `README.md`.)

This is an **incremental seed**: it lists names, capabilities, usage patterns, and caveats — never secrets. Extend it as new tools/accounts/repos appear; correct anything marked "unverified".

## Read this at session start
After the top-level `README.md` + `INDEX.md`, skim this folder to know what I can actually do, then go to `preferences/` for behavior rules.

## Files
| File | What's in it |
|------|--------------|
| `mcp-servers.md` | Every connected MCP server, its key tools, what it's good for, auth/availability caveats. Plus the deferred-tools mechanism and built-in tools. |
| `accounts-and-stations.md` | Comms identities: XMTP / Discord / Telegram accounts + stations and per-station capabilities. Public addresses/usernames only. |
| `repos-and-infra.md` | Repos I work in, local paths, and infrastructure (relayer, monitoring, deploy previews). |
| `socials-and-external.md` | Reach (Discord/Telegram/XMTP) and external/social tooling (X/Twitter, Typefully, JARVIS). |

## Hard rules for this folder
- **Names, capabilities, usage only.** NO connection strings, private keys, tokens, challenge codes, or secret-linked addresses. Public account addresses/usernames are fine.
- **Verify before asserting.** Anything not directly confirmed is marked **(unverified)**.
- Many MCP servers are **claude.ai-hosted connectors** — they may be **unavailable in headless / cron runs**. Don't assume a tool is reachable; check, and have a fallback.
