# Skill: diagnose an SX Starknet vote that didn't land

Trace end-to-end; confirm each hop before blaming the next.

1. **Identify the authenticator route** (EthSig vs EthTx vs StarkTx) for the user. EVM users default to EthSig; hardware wallets need EthTx. See `memory/snapshot-x-architecture.md`.
2. **L1 side**: confirm the `commit()` tx is mined on L1.
3. **L1→L2 message**: confirm consumed. On the EthTx authenticator (`0x63c89d1c...b442b0ea0`) check `_commits[hash][voter] == 0x1`. If set, the commit is valid & permanent.
4. **mana registration**: check for a `registered_transactions` row. **If the vote modal showed SUCCESS, the row WAS created** — the modal only flips to `success` after `await registerTransaction` resolves (failure re-throws → `fail`; `useActions.ts:82`, `mana.ts:15-33`, `rpc.ts:92-94`). So a success modal rules out the "lost POST" theory.
5. **L2 indexing**: confirm the L2 `authenticate` / vote indexed.

## #2186 corrected root cause — mana 24h sweep (NOT a lost POST)
- `apps/mana/src/stark/registered.ts:86` runs `markOldTransactionsAsProcessed()` every loop; `apps/mana/src/db.ts:47-51` force-sets `processed=true, failed=true` on ANY row older than 24h regardless of commit state.
- The `_commits == 0x0 → return` early-exit (`registered.ts:33`) leaves an in-flight row unprotected; the 24h sweep retires it before the L1→L2 commit lands. The commit later sets `_commits=0x1` but the `failed` row is never retried → stuck.
- **`_commits == 0x1` means RECOVERABLE** (commit valid & permanent) — a fix should keep retrying these, never auto-expire them. Only retire rows still at `0x0` past a window beyond worst-case L1→L2 latency; alert (don't silently drop) after 3 `client.vote` failures (`registered.ts:62-73`).
- Relayer reuses the stored hash+payload (no hash recompute) — recompute theory ruled out.
- Secondary silent-drop: `db.ts:22-32` `registerTransaction` uses `onConflict().ignore()` on `unique(sender, hash)`.
- To confirm on a stuck vote: the mana DB row will show `failed=true` while on-chain `_commits=0x1`.

**Gotcha:** an orphaned-but-valid commit can't be cheaply re-triggered — reproducing the commit hash needs the original `metadataUri`/reason text, not recoverable from chain.

**Signing errors:** for "invalid signature length" / Ledger rejections, get the **full error object** before diagnosing — the throw is often in MetaMask `KeyringController.signTypedMessage`, upstream of sx.js.
