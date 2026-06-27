# MCP: Zapier

**Server:** `claude.ai Zapier` (`mcp__claude_ai_Zapier__*`).
**Purpose:** Fronts **9,000+ apps** via Zapier as on-demand "actions" (incl. Gmail, Slack, Sheets, **Typefully**, X/Twitter, etc.). Actions are not pre-loaded — you discover an app's actions, enable the specific ones you want, then execute them (read or write).
**Auth note:** Available in this headless worker. OAuth to each downstream app is handled by Zapier; no tokens exposed here.

## Real captured context (2026-06-27)
`list_enabled_zapier_actions` → **`{"apps": []}`** — **NO actions currently enabled.** Hint returned: use `get_zapier_skill("zapier:onboarding")`, `discover_zapier_actions`, or `auto_provision_mcp` to set up. So today Zapier is connected but a blank slate; nothing can execute until an action is enabled.

## Flow (per server instructions)
1. `list_enabled_zapier_actions` — **always call FIRST.** Lists enabled apps + their exact action keys (action names are NOT guessable). **READ.**
2. `discover_zapier_actions` — search the 9,000+ catalog for an app/action when not enabled. **READ** (search only).
3. `enable_zapier_action` — add a discovered action to the enabled set. **WRITE** (mutates the enabled-action config).
4. `execute_zapier_read_action` — run an enabled action that reads (e.g. find/list/get). **READ** (but runs against a live downstream account — use deliberately).
5. `execute_zapier_write_action` — run an enabled action that writes/sends/creates (e.g. post a tweet, send email, draft Typefully). **WRITE / side-effecting — NEVER invoke in the loop.**

## Tool inventory
READ / discovery (safe):
- `list_enabled_zapier_actions` — list enabled actions. **READ.** (captured above)
- `discover_zapier_actions` — search catalog. **READ.**
- `get_configuration_url` — get the Zapier config URL. **READ.**
- `list_zapier_skills`, `get_zapier_skill` — list/read saved workflow "skills" (e.g. `zapier:onboarding`). **READ.**
- `execute_zapier_read_action` — execute an enabled READ action (live downstream read; deliberate use).

WRITE / mutating (documented — NEVER invoked):
- `enable_zapier_action` / `disable_zapier_action` — change which actions are enabled. **WRITE (config).**
- `execute_zapier_write_action` — perform a side-effecting downstream action (post/send/create/update). **WRITE / side-effecting.**
- `auto_provision_mcp` — auto-set-up actions from existing connections. **WRITE (config).**
- `create_zapier_skill` / `update_zapier_skill` / `delete_zapier_skill` — manage saved skills. **WRITE.**
- `send_feedback` — send feedback to Zapier. **WRITE (side effect, low risk).**

## Notes
- **Typefully** is reachable here (relevant to Amalio's gtm/content flow — see `contacts/amalio.md`), but Typefully caps ~15 posts/month so API push isn't the chosen path; he posts from the dashboard. Documented as a capability, not a task.
- Because nothing is enabled, any real use requires an `enable_zapier_action` (WRITE) first — out of scope for read-only loop ticks.
