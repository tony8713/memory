# Skill: diagnose an SX Starknet vote that didn't land

Trace end-to-end; confirm each hop before blaming the next.

1. **Identify the authenticator route** (EthSig vs EthTx vs StarkTx) for the user. EVM users default to EthSig; hardware wallets need EthTx. See `memory/snapshot-x-architecture.md`.
2. **L1 side**: confirm the `commit()` tx is mined on L1.
3. **L1→L2 message**: confirm consumed. On the EthTx authenticator (`0x63c89d1c...b442b0ea0`) check `_commits[hash][voter] == 0x1`. If set, the commit is valid & permanent.
4. **mana registration**: check for a `registered_transactions` row. If missing, the UI's fire-and-forget `registerTransaction` POST was lost → relayer never submits L2 `authenticate` → vote never indexes. This is the common failure (issue #2186).
5. **L2 indexing**: confirm the L2 `authenticate` / vote indexed.

**Gotcha:** an orphaned-but-valid commit can't be cheaply re-triggered — reproducing the commit hash needs the original `metadataUri`/reason text, not recoverable from chain.

**Signing errors:** for "invalid signature length" / Ledger rejections, get the **full error object** before diagnosing — the throw is often in MetaMask `KeyringController.signTypedMessage`, upstream of sx.js.
