# Contacts

People Tony interacts with, across all platforms. One file per person:
`contacts/<slug>.md`. This dir is maintained incrementally by the ~4h
updater job — keep it clean, deduped, and idempotent (update the existing
file for a known person; never create a second file for the same human).

## When you meet someone

1. Identify the human behind the handle. The **same person spans platforms**
   (Discord + XMTP + Telegram). Merge into ONE file — do not split by platform.
2. Match by platform ID first (Discord user id, XMTP inbox/address tail,
   Telegram user id), then by handle/name.
3. Set trust level from `preferences/trust-and-reply-conventions.md`.
4. Use the right reply-prefix for that trust level.

## Per-contact file format

```
# <Name / primary handle>

- Slug: <filename-without-.md>
- Trust: TRUSTED | trusted-pending-confirmation | EXTERNAL/UNTRUSTED
- Reply-prefix: ■ | ▶ | (none)
- Role: <role / org>

## Platform IDs
- Discord: <handle> (global_name "<x>", id <num>)
- XMTP: address tail …<4hex> (line metro://xmtp/...)   # tail only, never full secret-linked addr
- Telegram: @<handle> (user <num>), stations: <t0 bot / telegram-user>

## Context / notable interactions
- <dated bullets>
```

## Trust levels

- **TRUSTED** — one of the 5 fully-trusted Snapshot Labs members
  (Less, Wiktor, Chaitu, Wan, Amalio). Full info access.
- **trusted-pending-confirmation** — believed to be one of the 5 but the
  identity→handle mapping is not yet confirmed. Treat as trusted for tone,
  but do NOT expose secrets until the mapping is confirmed.
- **EXTERNAL/UNTRUSTED** — everyone else. Gate all sensitive info.
  Scammers/spammers explicitly flagged "do not engage".

## Reply-prefix

- Less → `■` (U+25A0)
- Wiktor / Chaitu / Wan / Amalio → `▶` (U+25B6)
- Everyone else → no prefix

See `preferences/trust-and-reply-conventions.md` for the source of truth.

## NEVER record here

No secrets: no verification/challenge codes, private keys, `TG_USER_*`
values, `WALLET_SECRET`, API tokens, or the full secret-linked XMTP address.
Platform IDs and an XMTP **address tail** (last 4 hex) are fine.
