# Channels & users index

Channels/conversations Tony is active in across Discord / Telegram / XMTP, with known participants. Built from this session's context — entries marked **(unverified)** are inferred and need a live `read`/`group_info`/`list_accounts` confirmation. **No secrets** (no tokens, keys, or token-in-URL auth values).

Trust legend: **trusted-5** Snapshot Labs members get a reply-prefix — Less = `■`; Wan, Chaitu, Wiktor (=Sekhmet), Amalio = `▶`. Everyone else: no prefix, gate sensitive info.

Discord guild: **955773041898573854** (Snapshot Labs).

## Discord

| Channel / line id | Name / purpose | Known participants | Notes |
|---|---|---|---|
| thread `1517956186929102869` | "Bug: enable to vote with Ledger on SX Starknet" (SX Ledger bug thread) | Less (■), Wan (▶), Chaitu (▶), Sekhmet/Wiktor (▶) | Home of the Ledger-vote work; PRs #2183 / #2189. |
| `1030772407226601522` | **#api** | Sekhmet/Wiktor (▶) active | k8s migration + Checkpoint updates posted here. |
| `1478881436168880218` | interns | (unverified) | |
| `1448596560010150009` | gtm | Amalio (▶), Less (■) | Amalio confirmed via Less @-mention here. Content/Typefully flow. |
| `1504226489359401221` | Less test / DM channel | Less (■) | Connectivity-test count-ping sweeps land here. |
| `1125390177888645231` | (purpose unverified) | (unverified) | |
| `1508453453930692669` | (purpose unverified) | (unverified) | |

## Telegram

| Line / id | Name / purpose | Known participants | Notes |
|---|---|---|---|
| `t0` user `25220238` | Less via Telegram bot station | Less (■, @bonustrack) | |
| telegram-user station | Less via TG user station | Less (■) | Restored after Jun 25 cutover regression. |

## XMTP

| Line | Participant | Notes |
|---|---|---|
| `metro://xmtp/x0/85e076a16839fc6431056bcdd15f68b3` | Less (■), address tail …ef8b | Verified. |

## Participant ID reference (cross-link to contacts/)

| Person | Trust / prefix | Discord global_name (id) | Other |
|---|---|---|---|
| Less | trusted-5, ■ | `bonustrack_` (238307675501232128) | TG @bonustrack (25220238); XMTP …ef8b → contacts/less.md |
| Wan | trusted-5, ▶ | `wa0x6e` | contacts/wan.md |
| Chaitu | trusted-5, ▶ | `chaituvr` | contacts/chaitu.md |
| Wiktor (=Sekhmet) | trusted-5, ▶ | `0cf5e` / "Sekhmet" (183666514920996864) | contacts/wiktor.md |
| Amalio | trusted-5, ▶ | `amaliohidalgo` (1285995520174592085) | contacts/amalio.md |

## Status
- **Partial / started.** Channel purposes + participant rosters for several Discord lines are unverified. Next deepening: confirm via metro `group_info` / `read` per line and `list_accounts`, then split high-traffic channels into per-channel files if useful.
