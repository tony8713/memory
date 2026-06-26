# Skill: diagnose an SX Starknet vote that didn't land

Trace end-to-end; confirm each hop before blaming the next.

1. **Identify the authenticator route** (EthSig vs EthTx vs StarkTx) for the user. EVM users default to EthSig; hardware wallets need EthTx. See `memory/snapshot-x-architecture.md`.
2. **L1 side**: confirm the `commit()` tx is mined on L1.
3. **L1→L2 message**: confirm consumed. On the EthTx authenticator (`0x63c89d1c...b442b0ea0`) check `_commits[hash][voter]`. **A SUCCESSFUL vote CLEARS `_commits` back to `0x0`** — the authenticator deletes the commit on consumption. So: `0x1` = committed-but-never-voted (stuck); `0x0` AFTER a confirmed index = success (NOT "never registered"). Do not misread a cleared `0x0` on an indexed vote as a failure.
4. **mana registration**: check for a `registered_transactions` row. **If the vote modal showed SUCCESS, the row WAS created** — the modal only flips to `success` after `await registerTransaction` resolves (failure re-throws → `fail`; `useActions.ts:82`, `mana.ts:15-33`, `rpc.ts:92-94`). So a success modal rules out the "lost POST" theory.
5. **L2 indexing**: confirm the L2 `authenticate` / vote indexed.

## #2186 FINAL root cause — a lost/non-durable DB write masked by silent success (PR #2189)
Three theories were tried; the first two are DISPROVEN — keep them documented so they aren't re-proposed:
- **NOT the mana 24h sweep** (`markOldTransactionsAsProcessed`). DB showed recent MAINNET rows succeeded; only old SEPOLIA rows failed — failure does not track the 24h boundary.
- **NOT hash zero-padding.** pedersen `getStorageVarAddress` is padding-agnostic: `addr('0x291…') === addr('0x0291…')` (same storage slot).

**Actual cause:** for the missing votes, the `registerTransaction` INSERT never persisted, yet mana logged "Registering transaction" with no error and returned success.
- Proof: Better Stack `mana` logs show the POST arrived; read-only DB shows no row; the decisive contrast is the processing-loop count — the vote that LANDED (row id 38) flips count `0→1` then "broadcasted successfully", while the missing votes NEVER reached `count:1` → the write never committed.
- Mechanism: a lost/non-durable write at the PlanetScale `psdb.cloud` Postgres gateway, MASKED by (1) `db.ts` `registerTransaction` `.onConflict().ignore()` swallowing the no-op, and (2) `rpc.ts` returning `rpcSuccess(true)` unconditionally with no read-back. UI sees ✅, relayer never sees the row, commit stuck at `_commits=0x1`.

**Log signature to look for:** "Registering transaction" present, no error, but the processing loop for that hash never reaches `count:1`.

**Fix (PR #2189, `fix/mana-register-transaction-verify-write`):**
- `db.ts`: `.onConflict(['sender','hash']).merge({ updated_at }).returning('id')` → return row/null instead of ignore.
- `rpc.ts`: read the row back via `getDataByMessageHash` after insert; `rpcError(500)` if absent, else `rpcSuccess`.
- Deferred: client/relayer retry on null read-back; knex pool `afterCreate` ping or txn-wrap the insert so a dropped psdb connection rejects.

**Gotcha:** an orphaned-but-valid commit can't be cheaply re-triggered — reproducing the commit hash needs the original `metadataUri`/reason text, not recoverable from chain.

**Signing errors:** for "invalid signature length" / Ledger rejections, get the **full error object** before diagnosing — the throw is often in MetaMask `KeyringController.signTypedMessage`, upstream of sx.js.
