# skill: Diagnose XMTP send outage — multi-client / installation-cap / shared-IP rate bucket

**When to use:** XMTP **sends** fail/time out while **reads still work** (`QueryGroupMessages` OK but outbound rejected). Discord/Telegram unaffected. Symptom often surfaces as a per-IP rate limit "resource exhausted" on `GetIdentityUpdatesV2` (e.g. node `R152…DEF`) that **persists across daemon restarts/reconnects**. (Observed 2026-06-29.)

This is a multi-layer failure — don't stop at the first cause. Work the chain in order; each step only matters if the prior didn't fix it.

## The failure chain (and fix sequence)

1. **Duplicate local XMTP client.** Two clients running on one inbox (e.g. a stray local daemon + the cloud one) multiply identity-update calls and burn rate budget. → **Remove the duplicate local client first.**

2. **Installation cap (10/inbox).** Each fresh client boot registers a new *installation*; XMTP caps an inbox at **10 installations**, and a near-cap inbox amplifies `GetIdentityUpdatesV2` traffic. → **Revoke stale installations** to bring the inbox back down (target ~**1/10**). Revocation is a real on-chain identity write — confirm before doing it.

3. **Still rate-limited after revocation?** Then the bucket isn't really about *your* installations.

4. **ROOT CAUSE (the deep one): the cloud client never boots at all.** If the cloud identity depends on a **raw-privateKey loader patch** and that patch is **only a local uncommitted edit** (not in the deployed image), the deployed code has **no reference to the key env var** (`XMTP_TONY_PRIVATE_KEY`) → the client silently never initializes → no sends. Meanwhile the per-IP **rate bucket is keyed on the host's shared egress IP** (e.g. a Fly machine's NAT egress IP), so it's **exhausted by neighbors / prior boots and a restart does NOT clear it**.
   - **Tell:** deployed source has no `XMTP_TONY_PRIVATE_KEY` ref; the loader patch lives only in a local working tree (`~/work/metro-protocol/packages/xmtp/src/accounts.ts`).
   - **Fix:** **deploy the loader patch** (get the local edit into the built image) **and set `DERIVE_COUNT=1`** (limit installation derivation to a single deterministic installation so boots stop minting new installations / hammering identity updates).

## Key mental model
- **Reads vs sends decouple:** reads use `QueryGroupMessages`; the identity/installation path (`GetIdentityUpdatesV2`) gates *sends* and registration. A read-OK / send-DOWN split points at the identity/installation layer, not message transport.
- **Shared-IP rate buckets don't reset on restart** — restarting the machine keeps the same egress IP and the same exhausted bucket. Fix the cause of the excess calls (dup clients, installation churn), don't just bounce the process.
- **An uncommitted local patch the whole identity depends on is a deploy-drift landmine** — "works locally" hides that the deployed image has no idea the key exists. Always check whether the env-var reference exists *in the deployed code*, not just locally.

## NO secrets
Never record the raw private key, the key env-var *value*, or any seed phrase here. Capability + env-var *names* and the fix sequence only.
