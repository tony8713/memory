# STATUS — open items (carried-forward)

Last updated: 2026-06-26 18:30. Newest report: reports/2026-06-26-1830.md.

## Open / next
- **#2186** (sx-monorepo) — mana self-register decision pending: register from `CommitAdded`/`_commits` on-chain, vs UI persist+retry. No fix chosen yet.
- **#2183** (`fix/ledger-vote-via-tx`) — APPROVE-WITH-NITS; ready to merge pending nits.
- **#2185** — UI split off #2181; merge after the #2181 backend lands.
- **Orphaned EthTx commits** — valid/permanent on L1 but never indexed on L2; no cheap re-trigger path (needs original metadataUri/reason, not recoverable from chain).
- **#636** (bonustrack/stage, ChatKit) — CI gate red (build passes; lint/typecheck/knip/madge/test fail). Testable via Expo preview + `stage://` deep links; not merge-ready.
- **Sekhmet → Wiktor / Amalio** identity mapping — trusted-pending-confirmation; not confirmed this session.

## Healthy / resolved recently
- All 3 stations live (telegram-user restored after the Jun 25 cutover regression).
- Stage transactions UI rendering cleanly (Less's 20k USDC History screenshot).
- Safe-signed Snapshot vote (gnosis.eth GIP-151, Safe 0xCa1c…BFFb) pushed to the hub and verified live; fully done, nothing carried forward.
