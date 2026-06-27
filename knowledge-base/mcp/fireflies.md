# MCP: Fireflies

**Server:** `claude.ai Fireflies` (`mcp__claude_ai_Fireflies__*`).
**Purpose:** Fireflies.ai meeting intelligence — transcripts, summaries, soundbites, analytics, channels, users; (write) create soundbites, share/move/update meeting privacy & titles.
**Auth note:** Logged in as **Tony** (`tony@bonustrack.co`, **Is Admin: true**). Available in this headless worker (`fireflies_get_user` succeeded). No tokens exposed.

## Real captured context (2026-06-27)
### Current user (`fireflies_get_user`)
- Name "Tony !", email tony@bonustrack.co, **admin**. No recent meeting/transcript, **0 minutes consumed**, no integrations on this account.
- → This Fireflies account itself has no captured meetings; org meeting data (if any) would be under other users/teams. Fireflies IS wired into the Notion workspace (bot present there).

## Tool inventory (schema-level only this tick — privacy: did NOT pull transcripts/PII)
READ-safe:
- `fireflies_get_user` — current/other user account details. **READ.** (captured self)
- `fireflies_get_user_contacts` — user's contacts. **READ — PII** (avoid).
- `fireflies_get_usergroups` — user groups. **READ.**
- `fireflies_get_transcript` / `fireflies_get_transcripts` — meeting transcript(s). **READ — PII/CONTENT** (do not bulk-pull).
- `fireflies_get_summary` — meeting summary. **READ — content.**
- `fireflies_get_soundbites` — soundbites. **READ — content.**
- `fireflies_search` — search across meetings/transcripts. **READ — content.**
- `fireflies_fetch` — fetch a resource by id. **READ.**
- `fireflies_get_active_meetings` — live meetings. **READ.**
- `fireflies_get_analytics` — usage/meeting analytics. **READ.**
- `fireflies_get_channel` / `fireflies_list_channels` — channels. **READ.**
- `fireflies_get_rule_executions` — automation rule runs. **READ.**

WRITE / mutating (documented — NEVER invoked):
- `fireflies_create_soundbite` — create a soundbite. **WRITE.**
- `fireflies_share_meeting` / `fireflies_revoke_meeting_access` — change sharing. **WRITE.**
- `fireflies_move_meeting` — move meeting to a channel. **WRITE.**
- `fireflies_update_meeting_privacy` / `fireflies_update_meeting_title` — modify meeting. **WRITE.**

## Notes
- Strong PII surface (meeting transcripts/summaries/contacts). KB captures only the account shape — never transcript bodies. Pull transcripts only on an explicit, scoped task.
- Tony is org admin here, so share/privacy WRITE tools are powerful — guard accordingly.
