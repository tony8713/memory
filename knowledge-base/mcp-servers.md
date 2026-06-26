# MCP servers & built-in tools

Connected MCP servers, their key tools, what each is good for, and auth/availability caveats. **Caveat for all claude.ai-hosted connectors:** they may be **unavailable in headless / cron runs** — verify reachability, have a fallback. Tool names below are indicative; load exact schemas via the deferred-tools mechanism (see bottom) before calling.

---

## metro — comms transport (the daemon/bot I operate through)
Send/receive across **Discord, XMTP, Telegram** from one interface. This is how Tony reaches people.

Key tools: `send` (text and/or `attachments`, optional `reply_to`), `reply` (quote a `message_id`), `react` / `unreact` (emoji on a `message_id`), `edit` / `delete` (a `message_id`), `read` (recent history), `dm`, `create_channel` / `close_channel`, `add_members` / `remove_members`, `set_channel_metadata`, `group_info`, `list_accounts`, `ask`.

Usage:
- Inbound arrives as `<channel source="metro" line="…" from="…" station="…" message_id="…">`. **Always pass the `line` attribute verbatim** — the station is derived from it.
- Inbound attachments are surfaced with an absolute `local_path` — `Read` that path to view.
- Station support varies per verb; the tool returns the daemon's reason if a verb is unsupported on that line. See `accounts-and-stations.md`.

**Reliability caveat (important):** the daemon is a **lossy transport** — it can **silently drop inbound** events and **lag sends ~300s** (an idle-timeout ERROR even when the message actually landed). **Read-back is the source of truth:** verify sends by reading the channel before resending, and reconcile inbound by sweeping. See `skills/metro-comms-reliability.md`.

---

## Better Stack — telemetry, logs, uptime
Query logs/metrics, build charts/dashboards, manage uptime monitors & incidents.

Key tools: `telemetry_query` (run queries), `telemetry_chart`, dashboard CRUD (`telemetry_list_dashboards` / `telemetry_get_dashboard_details` / `telemetry_create_dashboard` / `telemetry_export_dashboard`), source introspection (`telemetry_list_sources` / `telemetry_get_source_fields`), errors (`telemetry_list_errors`), uptime/incidents (`uptime_list_monitors`, `uptime_list_incidents`, `uptime_create_incident`, `uptime_resolve_incident`, on-call).

Good for: digging logs during incident diagnosis (e.g. mana relayer), checking indexer health, on-call/incident status.

Known IDs:
- **mana logs source id `1474369`**, table `mana_pino`.
- **SX Indexers dashboard id `1041825`**.

There are query-instruction helper tools (`telemetry_get_*_query_instructions`) — call them first to get the correct query DSL before composing a query.

---

## Zapier — 9,000+ app bridge
Read/write to thousands of SaaS apps (incl. **Typefully**, Gmail variants, sheets, etc.) without per-app MCPs.

Flow: `list_enabled_zapier_actions` (ALWAYS call first) → if the app/action isn't enabled, `discover_zapier_actions` → `enable_zapier_action` → then `execute_zapier_read_action` / `execute_zapier_write_action`. Saved workflows via `list_zapier_skills` / `get_zapier_skill` (e.g. `zapier:onboarding`).

Good for: X/Twitter posting, Typefully, and any long-tail SaaS not covered by a dedicated MCP.

---

## Snapshot — governance (reads + signed writes)
Snapshot governance over GraphQL plus signed actions.

Key tools: `snapshot-query` (GraphQL read), `snapshot-vote` (cast/replace a vote), `snapshot-propose` (create a proposal — only `space`, `title`, `body` required), `snapshot-whoami` (connected address + profile), `snapshot-follow` (follow/unfollow), `snapshot-schema` (introspect on demand).

Usage gotchas:
- The user's address is **auto-bound as `$user`** on every `snapshot-query` — declare `query Foo($user: String!)` and reference it; do **not** pass `user` in `variables`.
- Space `id` is a slug ("ens.eth"), never the display name.
- Timestamps are **unix seconds UTC** (not ms); verify the year before showing dates.
- Pagination caps: `first` ≤ 1000; `skip` ≤ 5000 on votes/proposals — cursor-paginate (`created_lt`) past that.
- Re-calling `snapshot-vote` on the same proposal **replaces** the prior vote.
- For Safe (EIP-1271) signed votes relayed to the sequencer, see `skills/push-safe-signed-snapshot-vote.md`.

---

## Tenderly — EVM simulation / tracing / virtual testnets
Simulate, trace, and debug EVM transactions; spin up vnets (virtual testnets).

Key tools: `simulate_transaction` / `trace_transaction`, vnet lifecycle (`create_vnet`, `fork_vnet`, `send_vnet_transaction`, `vnet_call`, `set_erc20_balance`, `fund_account`, `mine_block`, `increase_time`), rich trace/state inspection (`get_simulation_call_trace`, `get_simulation_state_changes`, `get_simulation_events`, `find_failures`), `get_contract_info`, `get_networks`.

Good for: reproducing/debugging SX on-chain failures, dry-running txs before relaying.

---

## Sentry — error monitoring
Issues/events search and triage.

Key tools: `find_organizations`, `find_projects`, `search_issues`, `search_events`, `get_sentry_resource`, `analyze_issue_with_seer`, `execute_sentry_tool`.

Caveat: **no `mana` project in Sentry.** `snapshot-relayer` in Sentry is a **different service** from the mana relayer — don't conflate them. For mana relayer logs use Better Stack (source `1474369` / `mana_pino`).

---

## Google / productivity connectors (claude.ai-hosted)
- **Gmail** — drafts, threads, labels (`search_threads`, `get_thread`, `create_draft`, label management). Note: read/draft/label oriented; sending may route via Zapier.
- **Google Calendar** — `list_events`, `create_event`, `update_event`, `respond_to_event`, `suggest_time`.
- **Google Drive** — `search_files`, `read_file_content`, `download_file_content`, `create_file`, `get_file_metadata`.
- **Notion** — search/fetch/create/update pages & databases, comments, meeting notes (`notion-search`, `notion-fetch`, `notion-create-pages`, `notion-query-data-sources`).
- **Intercom** — conversations, contacts, companies, help-center articles (`search_conversations`, `get_conversation`, `list_articles`, `create_article`).
- **Netlify** — deploy/project/team readers & updaters (`netlify-deploy-services-reader/updater`, `netlify-project-services-reader/updater`, `get-netlify-coding-context`). Good for checking Stage/Snapshot deploy previews.
- **Fireflies** — meeting transcripts/summaries/soundbites (`fireflies_get_transcripts`, `fireflies_get_transcript`, `fireflies_get_summary`, `fireflies_search`).
- **Browserbase** — headless browser automation (`start`, `navigate`, `act`, `observe`, `extract`, `end`). Good for scraping/driving a site no API covers.
- **Cloudflare Developer Platform** — requires `authenticate` / `complete_authentication` first.
- **Docusign** — requires `authenticate` / `complete_authentication` first.
- **Calendly** — requires `authenticate` / `complete_authentication` first.

---

## Snapshot databases (read)
- **Snapshot MySQL** (a.k.a. `Snapshot_MySQL`) — `mysql_list_tables`, `mysql_describe_table`, `mysql_read_query`. Read-only SQL into Snapshot's MySQL.
- The **mana relayer DB** is PostgreSQL on PlanetScale (psdb) — accessed during #2186 diagnosis via a read-only grant, **not** via these MySQL tools. No credentials stored anywhere in this repo. See `repos-and-infra.md` + `STATUS.md`.

---

## Deferred-tools mechanism (ToolSearch)
Most MCP tools above are **deferred**: only their names are loaded, not schemas. Calling one directly fails with InputValidationError. To use it, first run **`ToolSearch`** with `select:<name>[,<name>…]` (exact) or keyword query to load the schema, then call the tool. Deferred tool names appear in `<system-reminder>` messages.

## Built-in tools (always available, not MCP)
- **Agent / worker** — spawn background sub-agents (Opus, deep thinking) for hands-on + diagnosis work so the comms session stays responsive. Run in parallel; continue one via `SendMessage`.
- **Bash** — shell; supports background runs and worktree isolation.
- **File tools** — `Read`, `Edit`, `Write`, `NotebookEdit`.
- **Workflow / scheduling** — `Monitor`, `EnterWorktree`/`ExitWorktree`, plus skills like `loop` and `schedule` for recurring/cron agents.
- **Web** — `WebSearch`, `WebFetch`.
