# Channels & users index

Channels/conversations Tony is active in across Discord / Telegram / XMTP, with known participants. Built from this session's context — entries marked **(unverified)** are inferred and need a live `read`/`group_info`/`list_accounts` confirmation. **No secrets** (no tokens, keys, or token-in-URL auth values).

Trust legend: **trusted-5** Snapshot Labs members get a reply-prefix — Less = `■`; Wan, Chaitu, Wiktor (=Sekhmet), Amalio = `▶`. Everyone else: no prefix, gate sensitive info.

Discord guild: **955773041898573854** (Snapshot Labs).

## Discord

| Channel / line id | Name / purpose | Known participants | Notes |
|---|---|---|---|
| thread `1517956186929102869` | "Bug: enable to vote with Ledger on SX Starknet" (SX Ledger bug thread) | Wan (▶, `wa0x6e` 293785374063460355) very active; Metro responding | **Verified 2026-06-27** (metro read). Home of the Ledger-vote work; PRs #2183 / #2189. Wan @-mentions role `1477618545998172281`. |
| `1030772407226601522` | **#api** | Less (■, `bonustrack_`/less 238307675501232128), Sekhmet/Wiktor (▶, `0cf5e`/Sekhmet 183666514920996864) | **Verified 2026-06-27** (metro read). k8s migration + Checkpoint #390/#394 + #2125 split discussed here. |
| `1478881436168880218` | interns | Less (■ 238307675501232128), Sekhmet (▶ 183666514920996864), Chaitu (▶ `chaituvr` 497104796919267329), Wan (▶ 293785374063460355), `tonyshot26`/"Yooo" (1477276312430313503) | **Verified 2026-06-27** (metro read). Team banter + indexer/hub-restart pings; has sub-threads. `tonyshot26` = separate Tony Discord acct (no prefix). |
| `1448596560010150009` | gtm | Amalio (▶), Less (■) | Amalio confirmed via Less @-mention here. Content/Typefully flow. |
| `1504226489359401221` | Less test / DM channel | Less (■) | Connectivity-test count-ping sweeps land here. |
| `1125390177888645231` | **Snapshot Labs team / payment-feed channel** | Less (■ 238307675501232128), Amalio (▶ 1285995520174592085), Metro; bots: `snapshot` (892847850780762122) proposal-created embeds, `Schnaps` webhook (1360820059756433460) "New payment of N USDC" feeds | **Verified 2026-06-27** (metro read). Mixes payment notifications (Schnaps + Stage "received payment" FYIs) with team chat (Stage History screenshots, GTM/Notion research links). |
| `1508453453930692669` | **1:1 DM: tonyshot26 ↔ Metro bot** (personal setup/onboarding) | `tonyshot26`/"Yooo"/"Hery" (1477276312430313503), Metro bot (1502253402384633997) | **Verified 2026-06-27** (metro read). Private DM used for Metro setup (e.g. Firebase service-account onboarding) + identity-verification handshakes. Same `tonyshot26` acct seen in interns. NOTE: history contains a posted creds file — do not quote its contents. |

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
- **All known Discord lines now verified (2026-06-27).** The last two (`1125390177888645231` team/payment-feed; `1508453453930692669` tonyshot26↔Metro DM) confirmed via metro `read`; no `(unverified)` Discord rows remain. Each trusted-5 contact file carries a "Key channels" block pointing back here (contacts/ ↔ channels/ cross-linked both ways).
- Next (optional): split high-traffic channels into per-channel files if useful; otherwise maintenance mode (re-verify on drift).
