# Skill — Don't normalize addresses at the sequencer (ingestion already enforces EIP-55)

**When:** any sx-monorepo / snapshot.js change that proposes to `toLowerCase()` or otherwise "normalize" Ethereum addresses for storage or comparison.

## The rule
Addresses entering the system are already **EIP-55 checksummed** — ingestion validates them via snapshot.js `validateSchema` with `format: "address"`. The checksummed form is the **canonical** form everything downstream relies on.

- **Do NOT lowercase at the sequencer.** It regresses **vote dedup, leaderboard, follows, and moderation** (PK/lookup keys are the checksummed string). This is exactly why **sx PR #2169 was closed** (2026-07-01) — Wan's "this breaks comparisons" review was correct.
- For **comparisons**, compare in the canonical (checksummed) space, not by lowercasing both sides.

## Comparison helpers (state as of 2026-07-01)
- snapshot.js has **no equality helper** — only `getFormattedAddress` (normalization), no `isSameAddress`/`compareAddresses`.
- Monorepo has `compareAddresses` **copy-pasted** in `apps/ui` + `apps/auction` (not shared).
- Sequencer has **16 ad-hoc `toLowerCase()` comparison sites across 6 writer files**: delete-proposal, flag-proposal, settings, proposal, follow, moderation.
- Recommended fix (pending Wan's go): a single local **`isSameAddress`** helper to replace the ad-hoc sites — consistency without lowercasing stored data.

## ENS catch-block pattern (sx#2143, snapshot.js#1203)
When resolving ENS, **narrow the catch to `CALL_EXCEPTION`** (genuine "no record") and **re-throw infra errors** (RPC/network). Swallowing all errors as "no ENS" hides outages. Applied in sx#2143 (commit `b5686661`, approved); same narrowing requested by Wan on ChaituVR's snapshot.js#1203.
