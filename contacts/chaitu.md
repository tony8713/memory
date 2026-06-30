# Chaitu

- Slug: chaitu
- Trust: TRUSTED (one of the 5)
- Reply-prefix: ▶
- Role: Snapshot Labs engineer.

## Platform IDs
- Discord: `chaituvr` (global_name "Chaitu", id 497104796919267329)

## Key channels (see `knowledge-base/channels/README.md`)
- interns `1478881436168880218`; engaged on the SX Ledger / #2186 work.

## Context / notable interactions
- Hit the orphaned-EthTx-commit bug (#2186) with two votes on proposal 15. Stuck tx hashes: `0x6591768…`, `0x0291fd82…`, `0x04da4ce1…` (these rows never persisted — the lost-write cause).
- 2026-06-26: Told me to open the fix PR (#2189). Still has 2 stuck votes from the lost-write.
- 2026-07-01: **Owns snapshot.js#1203** (open, green/mergeable) — the **sequencer-side ENS fix**. Means we did NOT need a new PR for it. Only outstanding work is Wan's requested fallback-catch narrowing (narrow to `CALL_EXCEPTION` + re-throw infra, same pattern as our sx#2143). Awaiting Wan's call on whether we push that narrowing to Chaitu's branch.
