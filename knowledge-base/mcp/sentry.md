# MCP: Sentry

**Server:** `claude.ai Sentry` (`mcp__claude_ai_Sentry__*`).
**Purpose:** Error/issue tracking for Snapshot services. Find orgs/projects, search issues & events, inspect/triage issues, AI root-cause via Seer.
**Auth note:** Connected to org **snapshot-labs**. Available in this headless worker (read calls succeeded). No tokens exposed.

## Real captured context (2026-06-27)
### Organization (`find_organizations`)
- **snapshot-labs** — Web `https://snapshot-labs.sentry.io`, **regionUrl `https://us.sentry.io`** (pass as `regionUrl` to other tools; `organizationSlug` = `snapshot-labs`).

### Projects (`find_projects` for snapshot-labs) — 16
blockfinder, brovider, capsule, envelop, envelop-ui, keycard, laser, pineapple, score-api, **snapshot**, snapshot-hub, **snapshot-relayer**, snapshot-sequencer, snapshot-sidekick, snapshot-webhook, stamp.
- **NOTE: there is NO `mana` project in Sentry.** mana errors are not captured here — for mana use Better Stack (source `mana` 1474369) / PlanetScale Query Insights instead (cf. STATUS #2186).
- `snapshot-relayer` exists → relevant for the #2186 lost-write / relayer self-heal investigation.

## Tool inventory
READ-safe:
- `find_organizations` — list/search orgs. **READ.** (captured)
- `find_projects` — list/search projects in an org. **READ.** (captured)
- `search_issues` — search issues in a project/org (filter by status/level/query). **READ.**
- `search_events` — search individual events/errors. **READ.**
- `get_sentry_resource` — fetch a specific resource (issue/event/etc.). **READ.**
- `search_sentry_tools` — discover additional Sentry tool capabilities. **READ** (meta).
- `analyze_issue_with_seer` — AI root-cause analysis on an issue. **READ** (analysis; no state change, but can be slow/costly — use deliberately).

WRITE / mutating (documented — NEVER invoked):
- `execute_sentry_tool` — generic executor that can perform **write** operations (e.g. update issue status/assignment, resolve/ignore/mute, comment). Treat as **WRITE / mutating** — only invoke with explicit intent, never to "see what it does."

## Notes
- For mana / relayer DB-loss work, Sentry has the `snapshot-relayer` project but **not** mana — pair with Better Stack + PlanetScale.
- Region must be `us.sentry.io`.
