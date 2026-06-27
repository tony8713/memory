# MCP: Google Workspace (Gmail / Calendar / Drive)

**Servers:** `claude.ai Gmail` (`mcp__claude_ai_Gmail__*`), `claude.ai Google_Calendar` (`mcp__claude_ai_Google_Calendar__*`), `claude.ai Google_Drive` (`mcp__claude_ai_Google_Drive__*`).
**Purpose:** Tony's Google account access (tony@bonustrack.co) — email, calendar, files.
**Auth note:** Connected. **STUB — documented from schema only.** This tick deliberately did NOT read the user's actual mail/events/files (privacy). Real read-only context (e.g. `list_labels`, `list_calendars`, `list_recent_files`) to be captured in a later tick when warranted.

## Gmail
READ-safe (documented): `list_labels`, `list_drafts`, `search_threads`, `get_thread` — **READ.** (do not dump mail content into the repo).
WRITE / mutating (documented — NEVER invoked): `create_draft`, `create_label`, `update_label`, `delete_label`, `label_message`/`unlabel_message`, `label_thread`/`unlabel_thread`. **WRITE.**

## Google Calendar
READ-safe (documented): `list_calendars`, `list_events`, `get_event`, `suggest_time` — **READ.**
WRITE / mutating (documented — NEVER invoked): `create_event`, `update_event`, `delete_event`, `respond_to_event`. **WRITE.**

## Google Drive
READ-safe (documented): `list_recent_files`, `search_files`, `get_file_metadata`, `get_file_permissions`, `read_file_content`, `download_file_content` — **READ** (do not dump file content into the repo).
WRITE / mutating (documented — NEVER invoked): `create_file`, `copy_file`. **WRITE.**

## Notes
- All three are personal-data servers — apply extra restraint; capture only structural/label/calendar-name shape (never message bodies, event details, or file contents) into the KB, and only when there's a reason to.
