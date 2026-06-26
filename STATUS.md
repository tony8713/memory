# STATUS — open items (carried-forward)

Last updated: 2026-06-26 (post-1630). Newest report: reports/2026-06-26-1630.md (sequence: 1430 → 1830 → 1230 → 1630 — clock skew on the 1830/1230 stamps).

## Open / next
- **#2186** (sx-monorepo) — **FINAL root cause: a lost/non-durable write at the psdb.cloud gateway** (NOT the 24h sweep, NOT hash padding — both disproven by DB + pedersen). `registerTransaction` INSERT never persisted yet mana logged success; masked by `db.ts` `.onConflict().ignore()` + `rpc.ts` unconditional `rpcSuccess`. Proven via Better Stack logs + read-only DB + the processing-loop count never reaching `count:1`. Key on-chain fact: a SUCCESSFUL EthTx vote CLEARS `_commits` to `0x0`; `0x1` = stuck. **PR #2189 OPEN** (`fix/mana-register-transaction-verify-write`) — pending review/merge; CI may be red from unrelated `herodotus.ts` typecheck errors on master.
- **mana deferred follow-ups** — client/relayer retry when `getDataByMessageHash` returns null; knex pool `afterCreate` ping or txn-wrap the insert so a dropped psdb connection rejects instead of silently no-op'ing.
- **#2183** (`fix/ledger-vote-via-tx`) — **VERIFIED HEALTHY end-to-end** (voter 0x59a1748B…54Eb4, space 0x009fedaf…682a4 prop 15, mana row id 40, indexed ~4 min). Affected voter can vote on the PR deploy. Independent of #2186.
- **Metro daemon reliability** — OPEN operational item. Inbound message DROPS (1→10 sweep delivered only 1,2,5,6,9,10) + send/react 300s latency (false-error-but-sent). Mitigation: read-back to reconcile; daemon warrants restart/health check. See skills/metro-comms-reliability.md.
- **#2185** — UI split off #2181; merge after the #2181 backend lands.
- **Orphaned EthTx commits** — valid/permanent on L1 but never indexed on L2; no cheap re-trigger path (needs original metadataUri/reason, not recoverable from chain).
- **#636** (bonustrack/stage, ChatKit) — CI gate red (build passes; lint/typecheck/knip/madge/test fail). Testable via Expo preview + `stage://` deep links; not merge-ready.
- **Sekhmet → Wiktor** identity mapping — CONFIRMED 2026-06-26 (Sekhmet/`0cf5e` = Wiktor; contacts merged into `contacts/wiktor.md`, `contacts/sekhmet.md` removed). Amalio still unmapped.

## Healthy / resolved recently
- All 3 stations live (telegram-user restored after the Jun 25 cutover regression).
- Stage transactions UI rendering cleanly (Less's 20k USDC History screenshot).
- Safe-signed Snapshot vote (gnosis.eth GIP-151, Safe 0xCa1c…BFFb) pushed to the hub and verified live; fully done, nothing carried forward.
