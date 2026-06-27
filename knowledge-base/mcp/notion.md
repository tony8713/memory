# MCP: Notion

**Server:** `claude.ai Notion` (`mcp__claude_ai_Notion__*`).
**Purpose:** Snapshot Labs Notion workspace — search/read pages & databases, list users/teams, query data sources & meeting notes; (write) create/update/move/duplicate pages, comments, databases, views.
**Auth note:** Connected to the Snapshot Labs workspace; available in this headless worker (read calls succeeded). User-bound as Tony (`tony@bonustrack.co`). No tokens exposed.

## Real captured context (2026-06-27)
### Teams / teamspaces (`notion-get-teams`)
- **Joined:** `General` (id `e5d1f82a-69a8-433b-a2f8-6dc20cd34c51`, role member).
- **Other (mostly in_trash):** `✨ Home`, `🪤 Finance`, `🪰 Fireflies`, `🛠️ Product & Engineering`, `Private space` (not in trash, role none), `Welcome`.
- So the live workspace is small/consolidated — `General` is the active teamspace; the topical spaces are archived.

### Users (`notion-get-users`) — workspace members + bots
People (Snapshot Labs team, `@snapshot.org` emails unless noted):
- **Amalio** — amalio@snapshot.org
- **Chaitu** — chaitu@snapshot.org
- **Fabien** — fabien@snapshot.org
- **Wan** — wan@snapshot.org
- **Wiktor** — wiktor@snapshot.org
- **Tony** — tony@bonustrack.co (this account)

(IDs omitted here; not load-bearing. Cross-link to contacts/ by name. Note: Notion roster maps to the same Snapshot Labs people as Discord — trusted-5 = Less/Wan/Chaitu/Wiktor/Amalio; Fabien is an additional member not in the trusted-5.)

Bots/integrations present: Claude Code, Fireflies.ai, GrantDetectorBot, Notion MCP (x2), Schnaps, Tally Forms (x2), Tony (bot), Zapier (x2). → confirms Fireflies + Zapier + Tally are wired into Notion.

### Search shape (`notion-search "engineering roadmap"`)
Top hits: a **Roadmap** database (id `f39fa377…`), plus older pages (All hands, Basic, Vote, Other) from 2023–2024 → workspace has historical content; semantic search works.

## Tool inventory
READ-safe (captured or schema-confirmed read):
- `notion-search` — semantic/workspace search over Notion + connected sources (Slack/Drive/GitHub/Jira/Linear/Teams/SharePoint/OneDrive). `query_type` `internal` (content) or `user` (people). **READ.** (captured)
- `notion-get-users` — list/search workspace users (members, guests, bots) + IDs/emails/types; cursor-paginated. **READ.** (captured)
- `notion-get-teams` — list teamspaces (joined vs other), membership/role. **READ.** (captured)
- `notion-fetch` — fetch full contents of a page/database by id/URL. **READ.**
- `notion-get-comments` — read comments on a page/block. **READ.**
- `notion-query-data-sources` — query a data source (DB) with filters. **READ.**
- `notion-query-database-view` — query a specific DB view. **READ.**
- `notion-query-meeting-notes` — query the meeting-notes DB. **READ** (privacy: may surface meeting content — don't bulk-dump).

WRITE / mutating (documented — NEVER invoked):
- `notion-create-pages`, `notion-create-database`, `notion-create-view`, `notion-create-comment` — create.
- `notion-update-page`, `notion-update-data-source`, `notion-update-view` — update.
- `notion-move-pages`, `notion-duplicate-page` — move/duplicate.

## Notes
- Privacy: `notion-fetch` / `notion-query-meeting-notes` can expose private page/meeting bodies — capture only structure (titles/ids) in this KB, never page contents.
- Connected sources mean a Notion search can also reach Slack/Drive/GitHub etc. — useful as a cross-source index.
