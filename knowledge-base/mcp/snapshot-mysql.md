# MCP: Snapshot_MySQL

**Server:** `claude.ai Snapshot_MySQL` · **Status: Done (reachable, read-only schema captured 2026-06-27).**

Direct read-only SQL access to the **snapshot-hub** database (the legacy/offchain Snapshot hub — spaces, proposals, votes, users), connected to a **replica** (`snapshot-hub@replica`). This is the hub DB, NOT the Starknet/mana relayer DB (that one is the PlanetScale Postgres in `repos-and-infra.md` / `#2186`). Distinct data plane.

## Tools
| Tool | R/W | Notes |
|---|---|---|
| `mysql_list_tables` | **READ** | Lists all tables in the DB. Captured below. |
| `mysql_describe_table` | **READ** | Column/index/FK schema for one table. Captured for `proposals`, `votes`. |
| `mysql_read_query` | **READ** (read-only by contract) | Runs a SELECT. Name + description say read-only; treat as the query path for hub data. NOT invoked this tick to avoid pulling row data / PII (voter addresses, emails in `users`). Use deliberately with `LIMIT` when actually needed. |

No write/insert/update/delete tools are exposed — surface is read-only by design.

## Schema shape (table names only — NO row data dumped)
`snapshot-hub@replica`, **12 tables**: `aliases`, `follows`, `leaderboard`, `networks`, `options`, `proposals`, `skins`, `spaces`, `statements`, `subscriptions`, `users`, `votes`.

### `proposals` (34 cols)
PK `id` varchar(66). Key cols: `ipfs`, `author`, `created`, `space`, `network`, `type`, `strategies`/`validation`/`plugins`/`choices`/`labels` (json), `title`/`body`/`discussion`, `start`/`end`, `quorum`/`quorum_type`, `privacy`, `snapshot` (block), `app`, scoring block (`scores`, `scores_by_strategy`, `scores_state`, `scores_total`, `scores_updated`, `scores_total_value`, `vp_value_by_strategy`), `votes` (count), `updated`, `flagged`, `cb`. Heavily indexed for Explore-style listing (e.g. `idx_proposals_on_space_created_desc_id_asc`, `idx_proposals_on_end_desc_id`, `idx_proposals_on_scores_total_value`). Timestamps are unix seconds.

### `votes` (15 cols)
Composite PK **(voter, space, proposal)** — one vote per voter per proposal per space (re-voting overwrites). Cols: `id`, `ipfs`, `voter`, `created`, `space`, `proposal`, `choice` (json), `metadata` (json), `reason` (text), `app`, voting-power block (`vp`, `vp_by_strategy`, `vp_state`, `vp_value`), `cb`. Indexed for per-proposal + per-space reads (`idx_votes_on_space_proposal_created_id`, `idx_votes_on_proposal_vp_id`, `idx_votes_on_space_created_desc_id`).

> PII note: `users`/`aliases`/`subscriptions`/`statements` and the `voter`/`author`/`reason` columns carry addresses + user content. Describe-only is fine; do NOT dump rows. Use targeted SELECTs with LIMIT only when a task needs it.
