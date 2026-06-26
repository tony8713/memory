# MCP: Better Stack

**Server:** `claude.ai Better Stack` (`mcp__claude_ai_Better_Stack__*`).
**Purpose:** Telemetry/observability (log sources, dashboards, charts, metrics, errors) + Uptime (monitors, incidents, status pages, on-call, heartbeats). Used to inspect Snapshot infra health.
**Auth note:** Connected to the **Snapshot** team. Available in this headless worker (list calls succeeded). No tokens/keys exposed.

## Real captured context (2026-06-27)
### Sources (`telemetry_list_sources_tool`) — 38 on page 1, all 🟢 Active, team Snapshot
Confirmed key sources:
- **mana** — id **1474369** (javascript, eu-nbg-2) ✅ as expected
- sx-api: `sx-api-1` 1482977, `sx-api-2` 1482979, `sx-api-testnet-1` 1482981, `sx-api-testnet-2` 1482983
- sx indexers: `sx-api-indexer-1` 2363475, `sx-api-indexer-2` 2392882
- hub/seq/relayer: `snapshot-hub-mainnet` 1293346, `-testnet` 1293355, `snapshot-seq-mainnet` 1293364, `-testnet` 1293362, `snapshot-relayer` 1338497, `snapshot-webhook` 1338495
- schnaps-api 1291844 / -testnet 1291846; score-api 321901; delegate-registry-api 1026357; delegates-api-1 1375547
- supporting: brovider 1338485, blockfinder 1338487, subgrapher 1338491, envelop 1338493, laser 1338509, snapshot-sidekick 1338511, keycard 1338513, boost-guard 185688/1338501/-testnet 1338499, search-mainnet 1729202, brokester-attestations 1677134, **snapshot-mcp 2497178**, highlight-livenet/testnet, stamp, pineapple, DO sandbox 1490869
- (non-Snapshot demo: "Onboarding • Real-time flights" 2420004, team "Your team")

### Dashboards (`telemetry_list_dashboards_tool`) — 6
- **SX Indexers** — id **1041825** (created 2026-06-10, updated 2026-06-25) ✅ as expected
- Mana monitoring 521374; Wan's dashboard 166977; Chaitanya's dashboard 184721; Digital Ocean's dashboard 361463; Onboarding flights 1009780

## Tool inventory (families; ~90 tools total)
READ-safe (list/get/query/details/instructions):
- **Sources:** `telemetry_list_sources_tool`, `_get_source_details_tool`, `_get_source_fields_tool` — **READ.**
- **Dashboards:** `telemetry_list_dashboards_tool`, `_get_dashboard_details_tool`, `_export_dashboard_tool`, `_list_dashboard_templates_tool`, `_get_dashboard_query_instructions_tool` — **READ.**
- **Charts:** `telemetry_chart`, `_get_chart_details_tool`, `_get_chart_building_instructions_tool` — **READ.**
- **Query/metrics/errors:** `telemetry_query`, `_get_query_instructions_tool`, `_get_metric_details_tool`, `_get_metrics_and_cardinality_tool`, `_list_metric_expressions_tool`, `_list_errors_tool`, `_get_error_details_tool`, various `_get_*_query_instructions_tool` (explore-logs, errors, replays, metric) — **READ.**
- **Inventory:** `_list_applications_tool`, `_get_application_details_tool`, `_list_chart_alerts_tool`, `_get_chart_alert_details_tool`, `_list_clusters_tool`, `_list_data_regions_tool`, `_list_releases_tool`, `_list_teams_tool` — **READ.**
- **Uptime read:** `uptime_list_monitors_tool`, `_get_monitor_tool`, `_get_monitor_availability_tool`, `_get_monitor_response_times_tool`, `_list_incidents_tool`, `_get_incident_tool`, `_get_incident_timeline_tool`, `_get_incident_comments_tool`, `_list_heartbeats_tool`, `_get_heartbeat_tool`, `_get_heartbeat_availability_tool`, `_list_status_pages_tool`, `_get_status_page_tool`, `_list_on_calls_tool`, `_list_escalation_policies_tool`, `_list_severities_tool`, etc. — **READ.**

WRITE / mutating / destructive (documented — NEVER invoked in the loop):
- Telemetry create/edit/delete: `telemetry_create_source_tool`, `_create_dashboard_tool`, `_create_chart_alert_tool`, `_create_application_tool`, `_create_cloud_connection_tool`, `_create_metric_expression_tool`, `_add_chart_to_dashboard_tool`, `_add_dashboard_section_tool`, `_edit_chart_tool`, `_edit_chart_alert_tool`, `_edit_dashboard_tool`, `_edit_dashboard_section_tool`, `_move_charts_tool`, `_remove_chart_tool`, `_remove_dashboard_section_tool`, `_remove_dashboard_tool`, `_delete_chart_alert_tool`, `_delete_metric_expression_tool`, `_update_metric_expression_tool`, `_update_error_state_tool`, `_toggle_chart_alert_pause_tool`, `_import_dashboard_tool`. **WRITE/destructive.**
- Uptime mutating: `uptime_create_monitor_tool`, `_create_incident_tool`, `_create_incident_comment_tool`, `_acknowledge_incident_tool`, `_resolve_incident_tool`, `_reopen_incident_tool`, `_escalate_incident_tool`, `_create_status_page_report_tool`, `_create_status_page_report_update_tool`. **WRITE.**
- `better_stack_search_documentation_tool` — docs search. **READ** (helper).

## Notes
- Auth/availability: available in this headless worker. Source/dashboard ids are non-secret. No connection strings stored.
