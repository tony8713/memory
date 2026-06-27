# MCP: Intercom

**Server:** `claude.ai Intercom` (`mcp__claude_ai_Intercom__*`).
**Purpose:** Snapshot's Intercom — Help Center articles + support conversations/contacts/companies. Read articles & search KB; (write) create/update Help Center articles.
**Auth note:** Connected to Intercom workspace **`n9dmxtcs`** (Help Center at **help.snapshot.box**). Available in this headless worker (list_articles succeeded). No tokens exposed.

## Real captured context (2026-06-27)
### Help Center articles (`list_articles`)
- **59 published articles** across 12 pages (25/page default). Author id `7164173` on the sampled set.
- Sample titles (public Help Center, no PII): "How to enable gasless voting on a Governor space?", "What are the limitations for creating proposals on Snapshot?", "How to add a custom domain?", "oSnap deprecation & migration guide", "What is a space?".
- Articles live under collections (e.g. parent_id `9060007`, `9097457`); URLs like `https://help.snapshot.box/en/articles/<id>-<slug>`.
- List responses omit article bodies — fetch a single article (`get_article`) for full content.

## Tool inventory
READ-safe:
- `list_articles` — paginated Help Center article list (id/title/state/timestamps; no body). **READ.** (captured)
- `get_article` — full content of one article (body/markdown). **READ.**
- `search_articles` — search the Help Center. **READ.**
- `search` — generic search across Intercom resources. **READ.**
- `fetch` — fetch a specific resource by id. **READ.**
- `get_conversation` / `search_conversations` — support conversations. **READ — PII RISK** (customer messages); never bulk-dump.
- `get_contact` / `search_contacts` — end-user contacts. **READ — PII RISK** (emails/names); avoid.
- `get_company` / `list_companies` — companies. **READ** (may contain customer data).

WRITE / mutating (documented — NEVER invoked):
- `create_article` — create a Help Center article. **WRITE.**
- `update_article` — edit a Help Center article. **WRITE.**

## Notes
- Articles are public (help.snapshot.box) → safe to catalog titles. Conversations/contacts/companies carry customer PII → schema-documented only, not queried in this KB.
- Useful as the canonical support-content source (e.g. gasless voting, custom domains, oSnap migration) when answering user questions.
