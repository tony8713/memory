# BOOTSTRAP — re-arming Tony's loop after a session restart

The loop's behavior lives in [LOOP.md](LOOP.md) (in this repo, persistent). The SCHEDULER is a session-only cron that does NOT survive a restart — recreate it.

## To re-arm after a restart
Create a recurring hourly cron (CronCreate, `0 * * * *`, recurring:true) whose prompt is the self-directed pointer:

"Tony self-directed loop tick (hourly). Pull the memory repo (github.com/tony8713/memory) into the local clone, read the latest LOOP.md, and execute its instructions exactly (PART 1 inbox sweep — do yourself; PART 2 memory delta; PART 3 knowledge-base; PART 4 self-improvement). Delegate repo writes + MCP exploration to background workers; push to main. Quiet ticks = STATUS heartbeat only (no report churn)."

Then run one tick immediately.

Local clone path used this session: `/private/tmp/claude-501/-Users-tony-Cursor-bonustrack-stage/4dd3dd44-22e3-454c-bd6d-5cdadb147cf2/scratchpad/memory-repo` (a fresh session may have a different scratchpad path — if the clone is missing, `git clone https://github.com/tony8713/memory` into the session scratchpad first).

## Cadence history
- Currently HOURLY (`0 * * * *`). Previously 4h then 2h; tightened to hourly 2026-06-27.
- The dedicated 15-min SX-Ledger poll was retired 2026-06-27 (daemon recovered); re-arm a short-interval read-back poll only during daemon degradation or for a hot thread.

## Conventions reminder (also in LOOP.md / preferences/)
- Trust reply-prefixes: Less = ■; Wan/Chaitu/Wiktor(=Sekhmet)/Amalio = ▶; others none.
- React to acknowledge an actionable inbound before working; reply in the same channel; only reply when addressed to Tony/the team.
- NO secrets in the repo. Commit messages end with the `Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>` line.

## Pending when last session ended (2026-06-28)
- X MCP just installed (visible after restart) → like the 5 queued @SnapshotLabs tweets (URLs in the latest `reports/` + the #x channel `metro://discord/d0/1445027576006443109`) and add an X auto-like step to the loop for new Snapshot Labs posts.
- Open items awaiting humans: SX vote (Wan's `log_statement='mod'` decision), #2125 split (awaiting go), checkpoint#390 (review relayed). stamps #468/#469/#476 + Dependencies-update are the OTHER agent's.
