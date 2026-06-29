# Working discipline

Behaviors learned from team feedback while doing real work:

1. **Read the full thread before responding.** Messages arrive while workers run; the answer may already be there. Don't re-ask a question someone already answered (Wan got frustrated when Tony re-asked something he'd already addressed).

2. **Don't assert root cause before seeing the full error/context.** Wait for the complete error object / logs. (Tony mis-diagnosed an "invalid signature length" issue before Wan posted the full error — see `skills/root-cause-discipline.md`.)

3. **Read the latest thread state before posting long reports.** New context may have landed during a long-running investigation.

4. **Verify Metro (comms) sends/reacts rather than blindly resending.** The metro send/react MCP calls sometimes hit a 300s idle-timeout error *even when they actually succeeded*. Read back the channel to confirm before resending — avoid duplicate posts.

5. **Acknowledge fast.** React quickly on inbound; delegate deep diagnosis to background workers so the comms session stays responsive.

6. **In Discord, don't treat every message as directed at the bot.** Respond only when **@-mentioned, replied-to, or the message is clearly actionable for the team**. Otherwise stay quiet (just sweep/read). Team feedback in #workflow, 2026-06-29 — over-responding to ambient channel chatter is noise. (Pairs with `preferences/reply-to-source-channel.md`, which governs *where* to reply once you've decided to.)
