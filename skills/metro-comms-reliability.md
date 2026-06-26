# Skill: Metro comms reliability — the daemon can drop inbound + lag sends

The metro daemon is **not a reliable transport**. Treat push/inbound as lossy and verify everything against read-back.

## Two observed failure modes
1. **Send/react latency (false-error-but-sent):** a `send`/`react` call can take 300s and hit the MCP idle-timeout ERROR even though the message actually landed. **Do NOT blindly resend** — verify by reading the channel back, then resend only if genuinely absent. (Recurs across sessions.)
2. **Inbound message DROPS:** inbound events are silently lost. Confirmed with a 1→10 count sweep from Less where this session received only **1,2,5,6,9,10** — 3,4,7,8 never arrived. The daemon gave no indication of the gap. **Recurring and getting worse** (2026-06-26): later sweeps arrived as only **2,5,6,9,10** (twice), and a substantive line from Wan ("dig the db logs") was dropped and only recovered on read-back — drops hit real content, not just test counts.

## Operating rules
- **Read-back is the source of truth.** It is reliable even when push/inbound is lossy. Whenever a teammate says "you missed something" or pings "?", `read` the channel and reconcile rather than trusting your inbound feed.
- **Verify sends by reading the channel** before resending; assume a timeout may be a false error.
- **Consolidate reactions per burst** — one reaction per message burst, not per dropped/duplicated event, to avoid noise from the lossy feed.
- For critical asks, a re-ping helps surface drops.
- If drops/latency are frequent in a session, the daemon warrants a **restart / health check** — flag it as an operational item.
- **Read-back reconciliation is the reliable recovery** and is now baked into the recurring loop: the 2h loop opens with a **PART-1 INBOX SWEEP across all stations** to catch dropped messages before doing anything else.

## Comms etiquette — reply only when addressed
- **Only reply when the message is addressed to me (Tony) / the team.** In channels where humans talk to each other (e.g. the gtm channel), a message that isn't @-addressed to me (via the "Metro" bot account) is probably not for me.
- **Check the @-target before replying.** If it's directed at a specific person, stay out unless asked.
- Example (2026-06-26): I answered Less's "Do you use typefully?" but it was aimed at Amalio — Less corrected with **"Not asked u"**. Don't assume a non-addressed line in a human-to-human channel is for me.
