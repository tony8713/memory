# Always reply to the source channel

When a message arrives via a Metro `<channel>` tag, **always send the reply back to that same station/channel** using the messaging tools (`mcp__metro__send` / `reply`), passing the `line` attribute **verbatim**.

Do NOT write the response as plain main-session text — that text is invisible to the person on Discord/XMTP/Telegram and they get no answer.

Main-session prose is only for talking to the operator about orchestration, not for answering channel participants.

**Why:** Less explicitly corrected Metro after it composed a reply as plain text instead of sending it to the source Discord thread, so the recipient never saw it.

Pairs with the trust-prefix convention (`preferences/trust-and-reply-conventions.md`).
