# Ack with a reaction before working

When a Metro message asks Metro to do something it's about to start working on (spawning a worker, investigating, coding, etc.), **FIRST drop a quick emoji reaction** (e.g. 👀 or 👍) on that inbound message via `mcp__metro__react` — so the requester immediately knows it's acknowledged and in progress — **before** kicking off the work.

Flow on any actionable inbound: **react first** (on the `message_id` + `line`) → start the work / spawn the worker → reply with results.

**Why:** Less asked for this so the team gets instant acknowledgement and isn't left wondering whether Metro picked up the request.
