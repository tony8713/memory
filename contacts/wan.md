# Wan

- Slug: wan
- Trust: TRUSTED (one of the 5)
- Reply-prefix: ▶
- Role: Snapshot Labs engineer.

## Platform IDs
- Discord: `wa0x6e` (id 293785374063460355)

## Key channels (see `knowledge-base/channels/README.md`)
- **SX Ledger bug thread** `1517956186929102869` — very active; home of the Ledger-vote work (#2183/#2186/#2189). Has a dedicated 15-min read-back poll at his request.
- interns `1478881436168880218`.

## Context / notable interactions
- Drove the SX Starknet Ledger vote debugging (#2183, #2186).
- Caught the corrected #2186 root cause: pointed out the vote modal showed SUCCESS in every missing-vote case, which disproved the "lost POST" theory and pointed at the mana 24h sweep.
- Wants fast acknowledgements — ack before starting work.
- 2026-06-26: Drove the whole #2186 vote investigation. Caught that the modal showed success (disproved lost-POST) and also flagged the missing-message relay drop ("you're missing messages") during the connectivity sweep.
- 2026-06-27: Assigns tasks via **role-ping in the "tony" channel** (e.g. thread "Stamp - zod validation") AND via **#interns threads**; opened **stamp #468** (Add zod validation — Part 1, split from PR #451). Watch BOTH surfaces — the same issue was role-pinged in "tony" and assigned to a different user in #interns, so confirm ownership before implementing.
- 2026-06-26 (later): Pushed hard for the real root cause — "why is the insert failing" and the "dig the db logs" line that drove the read-only DB dig (which found the id-sequence gaps proving allocated-but-uncommitted inserts). Reframed #2189 as detection-not-recovery and helped shape the fix ladder (retry → relayer self-heal → write durability). Very engaged.
