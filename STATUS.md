# STATUS — open items (carried-forward)

Last updated: 2026-06-26 (post-1830). Newest report: reports/2026-06-26-1230.md (sequence after 1830 — clock skew on the stamp).

## Open / next
- **#2186** (sx-monorepo) — **CORRECTED root cause: mana 24h sweep**, not a lost POST. `markOldTransactionsAsProcessed` (`registered.ts:86` → `db.ts:47-51`) force-fails any row >24h even with an in-flight L1→L2 commit; commit lands (`_commits=0x1`) but the `failed` row is never retried. Modal-showed-success proves the row was created. Fix: never expire rows with `_commits=0x1` (recoverable); alert instead of silent-drop. Pending a fix PR + mana DB-read confirmation. NOT blocking the deadline.
- **mana DB read access** — READ-ONLY requested for tony (Wan→Less) to confirm `failed=true` on stuck rows (`0x6591768…`, `0x0291fd82…`, `0x04da4ce1…`) and monitor live. No credentials in repo.
- **#2183** (`fix/ledger-vote-via-tx`) — ready/testable on PR deploy `deploy-preview-2183--snapshot-livenet.netlify.app`. Deadline plan: affected voter votes there, Metro monitors end-to-end (fresh vote won't sit 24h). Independent of #2186.
- **#2185** — UI split off #2181; merge after the #2181 backend lands.
- **Orphaned EthTx commits** — valid/permanent on L1 but never indexed on L2; no cheap re-trigger path (needs original metadataUri/reason, not recoverable from chain).
- **#636** (bonustrack/stage, ChatKit) — CI gate red (build passes; lint/typecheck/knip/madge/test fail). Testable via Expo preview + `stage://` deep links; not merge-ready.
- **Sekhmet → Wiktor / Amalio** identity mapping — trusted-pending-confirmation; not confirmed this session.

## Healthy / resolved recently
- All 3 stations live (telegram-user restored after the Jun 25 cutover regression).
- Stage transactions UI rendering cleanly (Less's 20k USDC History screenshot).
- Safe-signed Snapshot vote (gnosis.eth GIP-151, Safe 0xCa1c…BFFb) pushed to the hub and verified live; fully done, nothing carried forward.
