# Accounts & stations (comms)

The comms identities Tony operates through the **Metro** daemon, across XMTP / Discord / Telegram. **Public addresses/usernames only** — no private keys, no secret-linked addresses, no `TG_USER_*` values.

> Recorded from known context. The live source of truth is `mcp__metro__list_accounts` (run by the comms session, not available to background workers). Reconcile against it when in doubt; mark drift here.

## XMTP
| Station | Address | Notes |
|---------|---------|-------|
| `x0` | `0xF7Bd…d26E` | XMTP account. |
| `x1` | (unverified address) | XMTP account. |
| `tony` | `0x0bA0…651A` | **Owner-bound** identity (raw-privateKey). The "Tony" XMTP identity. |

Capabilities (XMTP stations): **react, read, reply, send.** (No edit/delete on XMTP.)

## Discord
| Station | Username | userId |
|---------|----------|--------|
| `d0` | **Metro** | `1502253402384633997` |

The Discord **bot account is literally named "Metro"** — that's the account I post through, not my own identity. Capabilities (Discord): **full** — send, reply, react/unreact, edit, delete (plus channel/member management per the `metro` tools).

## Telegram
| Station | Handle | Notes |
|---------|--------|-------|
| `t0` | `metro30593bot` | Telegram bot. |
| `t1` | `bobmetrobot` | Telegram bot. |
| `t2` | `alicemetrobot` | Telegram bot. |
| `telegram-user` | (Less's personal account) | **User** station, not a bot. Restored after `TG_USER_*` creds were re-added (Jun 25 cutover regression fixed). |

Capabilities (Telegram): **edit, react, reply, send, delete.**

## Per-station capability summary
- **Discord (`d0`):** full incl. react / edit / delete + channel/member mgmt.
- **XMTP (`x0`/`x1`/`tony`):** react / read / reply / send.
- **Telegram (`t0`/`t1`/`t2`/`telegram-user`):** edit / react / reply / send / delete.

If a verb isn't supported on a line, the `metro` tool returns the daemon's reason — don't assume; check the return.

## Identity disambiguation
- **Tony** = me, the assistant (this repo's owner identity).
- **Metro** = the comms daemon + the Discord bot account named "Metro" I post through.
- **`tonyshot26`** ("Yooo") on Discord = a different human (handle "Tony"), not me. See `preferences/trust-and-reply-conventions.md`.
