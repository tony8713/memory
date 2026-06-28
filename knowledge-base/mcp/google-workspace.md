# MCP: Google Workspace (Gmail / Calendar / Drive)

**Servers:** `claude.ai Gmail` (`mcp__claude_ai_Gmail__*`), `claude.ai Google_Calendar` (`mcp__claude_ai_Google_Calendar__*`), `claude.ai Google_Drive` (`mcp__claude_ai_Google_Drive__*`).
**Purpose:** Tony's Google account access (tony@bonustrack.co) — email, calendar, files.
**Auth note:** Connected & REACHABLE (verified 2026-06-28 via structure-only read calls). Personal-data servers — capture only structural shape (label names, calendar names, counts), NEVER message bodies / event details / file contents.

## Gmail
READ-safe: `list_labels`, `list_drafts`, `search_threads`, `get_thread` — **READ** (never dump mail content into the repo).
WRITE / mutating (documented — NEVER invoked): `create_draft`, `create_label`, `update_label`, `delete_label`, `label_message`/`unlabel_message`, `label_thread`/`unlabel_thread`, `apply_sensitive_message_label`, `apply_sensitive_thread_label`. **WRITE.**
- **`list_labels` (verified 2026-06-28): returns `{}` — NO user-defined labels.** Only well-known system labels are usable by id: `INBOX`, `TRASH`, `SPAM`, `STARRED`, `UNREAD`, `IMPORTANT`, `CHAT`, `DRAFT`, `SENT`. Any `label_*` call must target a system-label id; there's no custom-label taxonomy to reference.

## Google Calendar
READ-safe: `list_calendars`, `list_events`, `get_event`, `suggest_time` — **READ.**
WRITE / mutating (documented — NEVER invoked): `create_event`, `update_event`, `delete_event`, `respond_to_event`. **WRITE.**
- **`list_calendars` (verified 2026-06-28): 2 calendars** — (1) `tony@bonustrack.co` (primary, timeZone UTC); (2) `Holidays in Thailand` (`en-gb.th#holiday@group.v.calendar.google.com`, read-only holiday feed, UTC). Account TZ is UTC. No other shared/secondary calendars.

## Google Drive
READ-safe: `list_recent_files`, `search_files`, `get_file_metadata`, `get_file_permissions`, `read_file_content`, `download_file_content` — **READ** (never dump file content/names into the repo).
WRITE / mutating (documented — NEVER invoked): `create_file`, `copy_file`. **WRITE.**
- Deliberately NOT exercised: even file *names* in Drive can be sensitive, so Drive stays schema-only. List/read on demand only when a task needs it.

## Notes
- All three are personal-data servers — apply extra restraint; only structural shape (label/calendar names, counts) goes into the KB, never message bodies, event details, or file contents, and only when there's a reason to.
- The two `apply_sensitive_*` Gmail tools mark a message/thread as sensitive (a labeling write) — documented, never invoked.
