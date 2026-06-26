# Skill: Metro comms reliability — the daemon can drop inbound + lag sends

The metro daemon is **not a reliable transport**. Treat push/inbound as lossy and verify everything against read-back.

## Two observed failure modes
1. **Send/react latency (false-error-but-sent):** a `send`/`react` call can take 300s and hit the MCP idle-timeout ERROR even though the message actually landed. **Do NOT blindly resend** — verify by reading the channel back, then resend only if genuinely absent. (Recurs across sessions.)
2. **Inbound message DROPS:** inbound events are silently lost. Confirmed with a 1→10 count sweep from Less where this session received only **1,2,5,6,9,10** — 3,4,7,8 never arrived. The daemon gave no indication of the gap.

## Operating rules
- **Read-back is the source of truth.** It is reliable even when push/inbound is lossy. Whenever a teammate says "you missed something" or pings "?", `read` the channel and reconcile rather than trusting your inbound feed.
- **Verify sends by reading the channel** before resending; assume a timeout may be a false error.
- **Consolidate reactions per burst** — one reaction per message burst, not per dropped/duplicated event, to avoid noise from the lossy feed.
- For critical asks, a re-ping helps surface drops.
- If drops/latency are frequent in a session, the daemon warrants a **restart / health check** — flag it as an operational item.
