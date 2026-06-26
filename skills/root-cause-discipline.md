# Skill: root-cause discipline

Hard-won lessons on not getting root cause wrong:

1. **Get the full error object / logs before asserting a cause.** The "invalid signature length" bug was first mis-attributed to sx.js `getRSVFromSig` (65 vs Ledger 64-byte EIP-2098). After Wan posted the full error it turned out to be MetaMask `KeyringController.signTypedMessage` throwing upstream (Ledger `UNKNOWN_ERROR 0x6a00`). The first guess was plausible and wrong.

2. **Distinguish "valid but stuck" from "failed".** An EthTx commit can be on-chain, valid, and permanent yet never index because a downstream POST was lost. Don't call a confirmed on-chain action "failed".

3. **A monitoring 0 is not proof of health.** A silent-count alert can decay 1→0 once a chain fully stops. Verify against the actual data source.

4. **Confirm each hop in a pipeline** rather than reasoning about the whole chain at once. Trace L1 → L1→L2 → mana → L2 individually.

5. **Reproduce when possible** (headless repro confirmed `domain={}` for the Ledger EIP-712 bug) instead of relying on code-reading alone.

6. **Reconcile a hypothesis against ALL observable facts — especially user-reported UI state — before asserting.** Two confident wrong diagnoses in the SX saga, both broken by a teammate-supplied fact contradicting a single-source read:
   - "invalid signature length = our `getRSVFromSig`" → actually MetaMask `KeyringController` (corrected by the full error object).
   - "#2186 = lost `registerTransaction` POST" → actually the mana 24h `markOldTransactionsAsProcessed` sweep (corrected by "the vote modal showed SUCCESS", which proves the POST returned 200 and the row was created).
   Don't over-trust one read (e.g. a `getDataByMessageHash` that returned unauthorized/ambiguous). A success-modal is hard evidence the client-side write succeeded — believe it and look downstream.
