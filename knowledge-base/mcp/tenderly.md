# MCP: Tenderly

**Server:** `claude.ai Tenderly` (`mcp__claude_ai_Tenderly__*`).
**Purpose:** EVM tx simulation, debugging/tracing, and Virtual TestNets (vnets) — simulate/trace transactions, inspect call traces / state & balance changes / events / gas, fund accounts and manipulate vnet state for testing.
**Auth note:** Available in this headless worker (read calls succeeded). Account id `b21ca741-…-f5aab82f383f`. No tokens exposed.

## Real captured context (2026-06-27)
### Projects (`list_projects`) — 1
- **Project** (slug `project`, id `bc25be61-19a4-41f1-b07e-5f780ebdaeb0`) — the lone default project. No Snapshot-specific named projects here; this looks like a generic/default workspace, not a curated SX setup.

### Networks (`get_networks`) — ~100 public chains supported
Includes all SX-relevant chains: **Mainnet (1)**, Arbitrum One (42161), OP Mainnet (10), Base (8453), Polygon (137), Gnosis Chain (100), Linea (59144), Scroll (534352), ZKsync Era (324), plus many L2s/testnets (Sepolia 11155111, Gnosis Chiado 10200, Base Sepolia 84532, Arbitrum Sepolia 421614, etc.). (`get_networks` is cheap; `search` param does loose name matching.)

## Tool families
### READ-safe — simulation/trace INSPECTION (read existing sim/tx results)
- Discovery: `list_projects`, `get_networks`, `get_user_info`, `get_contract_info`, `list_vnets`, `get_vnet`, `set_active_project`/`set_active_vnet` (selectors; non-destructive context switches). **READ.**
- Tx/sim inspection (mainnet sims & real txs): `get_simulation_call_trace`, `_state_changes`, `_balance_changes`, `_asset_transfers`, `_events`, `_exposure_changes`, `_gas_breakdown`, `_generated_access_list`; `get_call_trace_node`, `get_subtree_events`, `get_subtree_state_changes`, `get_trace_skeleton`, `get_trace_stats`, `get_error_path`, `get_transaction_fund_flow`, `search_call_trace`, `find_failures`. **READ.**
- Vnet inspection (mirror set, `get_vnet_*`): `get_vnet_simulation_call_trace/_state_changes/_balance_changes/_asset_changes/_events/_gas_breakdown/_generated_access_list`, `get_vnet_call_trace_node`, `get_vnet_subtree_events`, `get_vnet_trace_skeleton/_trace_stats`, `get_vnet_error_path`, `get_vnet_transactions`, `search_vnet_call_trace`, `find_vnet_failures`. **READ.**

### WRITE / mutating / state-changing (documented — NEVER invoked)
- **Simulation (executes a sim, no chain side-effect but creates a sim record / can be costly):** `simulate_transaction`, `trace_transaction`, `resimulate_transaction`, `simulate_vnet_transaction`, `trace_vnet_transaction`, `resimulate_vnet_transaction`, `vnet_call`, `vnet_multicall`. Treat as **WRITE-ish / side-effecting** — do not invoke to explore.
- **Vnet lifecycle:** `create_vnet`, `delete_vnet`, `fork_vnet`, `revert_vnet`, `snapshot_vnet`. **WRITE/destructive.**
- **Vnet state manipulation:** `send_vnet_transaction` (sends a real tx on the vnet), `fund_account`, `set_erc20_balance`, `set_storage_at`, `mine_block`, `increase_time`. **WRITE/mutating.**

## Notes
- Only the default "Project" exists; not a curated Snapshot infra workspace. Networks coverage is broad (~100 chains).
- Any simulate/trace/vnet call has side effects or cost — strictly documented-not-tested in the loop.
