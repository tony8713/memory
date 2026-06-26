# Skill: root-cause discipline

Hard-won lessons on not getting root cause wrong:

1. **Get the full error object / logs before asserting a cause.** The "invalid signature length" bug was first mis-attributed to sx.js `getRSVFromSig` (65 vs Ledger 64-byte EIP-2098). After Wan posted the full error it turned out to be MetaMask `KeyringController.signTypedMessage` throwing upstream (Ledger `UNKNOWN_ERROR 0x6a00`). The first guess was plausible and wrong.

2. **Distinguish "valid but stuck" from "failed".** An EthTx commit can be on-chain, valid, and permanent yet never index because a downstream POST was lost. Don't call a confirmed on-chain action "failed".

3. **A monitoring 0 is not proof of health.** A silent-count alert can decay 1→0 once a chain fully stops. Verify against the actual data source.

4. **Confirm each hop in a pipeline** rather than reasoning about the whole chain at once. Trace L1 → L1→L2 → mana → L2 individually.

5. **Reproduce when possible** (headless repro confirmed `domain={}` for the Ledger EIP-712 bug) instead of relying on code-reading alone.

6. **Reconcile a hypothesis against ALL observable facts — especially user-reported UI state — before asserting.** The SX saga had **THREE successive root-cause revisions**, each corrected only by a teammate-supplied fact or fresh data:
   - "invalid signature length = our `getRSVFromSig`" → actually MetaMask `KeyringController` (corrected by the full error object).
   - #2186 chain: "lost `registerTransaction` POST" → "mana 24h `markOldTransactionsAsProcessed` sweep" → **FINAL: a lost/non-durable write at the psdb.cloud gateway**, masked by `onConflict().ignore()` + unconditional `rpcSuccess`. The 24h-sweep theory was disproven by the DB (failures tracked Sepolia, not the 24h boundary); a zero-padding theory was disproven because pedersen `getStorageVarAddress` is padding-agnostic. The lost-write was nailed by reconciling logs (POST logged) + DB (no row) + the processing-loop count never reaching `count:1`.
   **Lesson:** present evidence + ranked hypotheses; reconcile against ALL observable facts (modal state, logs, DB, on-chain) before asserting. A single ambiguous read (e.g. a `getDataByMessageHash` returning unauthorized) is NOT proof. A success-modal proves the client write returned 200 — but NOT that the row durably persisted; look at whether downstream actually SAW the row (here, the loop count flip 0→1).

7. **On-chain state can be cleared on success — don't read absence as failure.** A successful EthTx vote CLEARS `_commits` back to `0x0` (commit deleted on consumption). `0x1` = stuck; `0x0` after a confirmed index = success. An earlier read treated cleared `0x0` as "never registered" — wrong.
