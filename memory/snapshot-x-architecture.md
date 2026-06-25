# Snapshot X (SX) Starknet architecture

## Authenticators (how a vote gets authorized)
- **EthSig** — EVM user signs an EIP-712 typed message; relayer submits the L2 authenticate. Default route for EVM users.
- **EthTx** — user sends a real **L1 `commit()` tx** (no typed-data); the commit hash is later consumed L1→L2 and the relayer submits the L2 authenticate. The route for hardware wallets that can't sign SX's empty-domain typed data.
- **StarkTx** — native Starknet tx.

## Mana relayer (shared)
- EthSig, EthTx, and StarkTx all share the **SAME per-space mana relayer account** (derived per space, STRK-funded).
- **EthTx fee split**: user pays the L1 `commit()` in ETH; the relayer pays the L2 `authenticate` in STRK. So enabling EthTx adds **no extra STRK burden** beyond what EthSig already costs.

## EthTx L1→L2 vote flow (and the failure mode)
1. User sends L1 `commit()` → confirmed on L1.
2. L1→L2 message consumed → on the EthTx authenticator (`0x63c89d1c...b442b0ea0`), `_commits[hash][voter] = 0x1`. Commit is now valid & permanent.
3. **UI fires a fire-and-forget `registerTransaction` POST to mana.** If this POST is lost, no `registered_transactions` row is created, the relayer never submits the L2 `authenticate`, and the vote never indexes on L2.
4. Result: an **orphaned-but-valid** commit. Re-triggering is blocked because reproducing the on-chain commit hash needs the original `metadataUri`/reason text, which is **not recoverable from chain**.

Tracking issue: **snapshot-labs/sx-monorepo #2186** — proposes mana self-register from `CommitAdded`/`_commits`, or UI persist+retry.

## Known bug: EVM Ledger voting on SX Starknet
EVM users are routed to EthSig, which signs an **empty-domain (`domain={}`) EIP-712** message that Ledger rejects (MetaMask `KeyringController.signTypedMessage` throws before sx.js sees anything; Ledger `UNKNOWN_ERROR 0x6a00`). Blind signing can't reliably fix EthSig. The real fix = route through EthTx.
- Fix PR: **#2183** (`fix/ledger-vote-via-tx`) — opt-in "Vote with a transaction" toggle.
- NOTE: the "invalid signature length" red herring (65-byte vs Ledger 64-byte EIP-2098 in `getRSVFromSig`) was a mis-diagnosis — the error is upstream in MetaMask, not sx.js.

## Repos
- `snapshot-labs/sx-monorepo` — `apps/ui` (UI), `sx.js` (SDK). UI can have **hard compile-time deps** on sx.js changes (relevant when splitting PRs).
