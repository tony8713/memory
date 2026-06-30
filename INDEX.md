# Index

Map of everything in this repo (Tony's memory). I am Tony; **Metro** is the comms daemon/bot account I operate through.

## Start here
- [README.md](README.md) — what this repo is.
- [LOOP.md](LOOP.md) — my self-editable **hourly** loop prompt (inbox sweep → memory delta → knowledge-base expansion → self-improvement). The scheduler just pulls this repo and runs it.
- [STATUS.md](STATUS.md) — carried-forward open items / next steps.
- [BOOTSTRAP.md](BOOTSTRAP.md) — how to re-arm the loop after a session restart (recreate the session-only cron).

## Knowledge base
- [knowledge-base/README.md](knowledge-base/README.md) — catalogue of all context + tooling; read at session start.
- [knowledge-base/PROGRESS.md](knowledge-base/PROGRESS.md) — self-improvement tracker (Mapped / Partial / TODO + next focus). **Read first in PART 3 of the loop.**

### Top-level KB files
- [accounts-and-stations](knowledge-base/accounts-and-stations.md) — XMTP/Discord/Telegram accounts, stations, per-station capabilities.
- [repos-and-infra](knowledge-base/repos-and-infra.md) — repos, local paths, relayer/monitoring/deploy infra.
- [socials-and-external](knowledge-base/socials-and-external.md) — reach + X/Typefully/JARVIS.
- [mcp-servers](knowledge-base/mcp-servers.md) — thin **index/pointer** into `mcp/` (server → file map). Full detail is per-server below.
- [built-in-tools](knowledge-base/built-in-tools.md) — non-MCP harness tools (Agent/Bash/Read/Write/Edit/ToolSearch/Skill/Monitor/Web*) + the Skill list.
- [hardening-plan](knowledge-base/hardening-plan.md) — prioritized Metro reliability hardening plan (P0 MCP single-transport/rebind wedge + retire local dispatcher; P1 deploy preflight, inbound heartbeat, reconnect backfill; P2 identity pinning, telegram-user). From the 2026-06-28 incident.

### MCP capability map — [knowledge-base/mcp/](knowledge-base/mcp/README.md) (one file per server, READ-safe vs WRITE flagged)
- [metro](knowledge-base/mcp/metro.md) · [snapshot](knowledge-base/mcp/snapshot.md) · [better-stack](knowledge-base/mcp/better-stack.md) · [zapier](knowledge-base/mcp/zapier.md) · [sentry](knowledge-base/mcp/sentry.md) · [tenderly](knowledge-base/mcp/tenderly.md)
- [notion](knowledge-base/mcp/notion.md) · [intercom](knowledge-base/mcp/intercom.md) · [netlify](knowledge-base/mcp/netlify.md) · [fireflies](knowledge-base/mcp/fireflies.md) · [snapshot-mysql](knowledge-base/mcp/snapshot-mysql.md)
- [google-workspace](knowledge-base/mcp/google-workspace.md) (stub) · [oauth-gated-and-misc](knowledge-base/mcp/oauth-gated-and-misc.md) (Cloudflare/Calendly/Docusign/Slash/Browserbase)

### Channels & users — [knowledge-base/channels/](knowledge-base/channels/README.md)
- Every Discord/Telegram/XMTP channel Tony is in + its participants (handle/id/role/trust/prefix), cross-linked to contacts/.

## Preferences (behavior rules — read early)
- [trust-and-reply-conventions](preferences/trust-and-reply-conventions.md) — the 5 trusted members, reply-prefix scheme, info gating.
- [reply-to-source-channel](preferences/reply-to-source-channel.md) — always answer in the originating Metro channel.
- [ack-with-reaction](preferences/ack-with-reaction.md) — react before starting work.
- [working-discipline](preferences/working-discipline.md) — read full thread / full error before responding; verify MCP sends.

## Memory (durable facts)
- [people-and-team](memory/people-and-team.md) — Snapshot Labs team, Discord/identity mapping.
- [metro-architecture](memory/metro-architecture.md) — Metro inbound (cloud vs local), trains, allowlist.
- [snapshot-x-architecture](memory/snapshot-x-architecture.md) — SX Starknet authenticators, mana relayer, L1→L2 flow.
- [infra-and-monitoring](memory/infra-and-monitoring.md) — indexers, droplets, Better Stack alerts.

## Skills (playbooks)
- [diagnose-sx-vote-pipeline](skills/diagnose-sx-vote-pipeline.md) — trace an SX Starknet vote that didn't land (incl. the #2186/#2189 lost-write root cause).
- [split-pr-by-ui-vs-backend](skills/split-pr-by-ui-vs-backend.md) — split a PR into UI vs backend stacked branches.
- [root-cause-discipline](skills/root-cause-discipline.md) — confirm each hop before blaming the next.
- [push-safe-signed-snapshot-vote](skills/push-safe-signed-snapshot-vote.md) — relay a Safe (EIP-1271) signed Snapshot vote to the sequencer.
- [metro-comms-reliability](skills/metro-comms-reliability.md) — read-back is source of truth; the daemon drops inbound + lags sends.
- [xmtp-multi-client-installation-cap](skills/xmtp-multi-client-installation-cap.md) — XMTP send-outage failure mode (dup client → installation cap → shared-IP rate bucket → missing deployed loader patch) + fix sequence.
- [sx-address-normalization-checksum](skills/sx-address-normalization-checksum.md) — don't lowercase addresses at the sequencer (ingestion enforces EIP-55; #2169 closed); ENS catch-narrowing pattern.

## Contacts — [contacts/README.md](contacts/README.md) (one file per person; platform IDs, trust, prefix)
- [less](contacts/less.md) (■) · [wan](contacts/wan.md) (▶) · [chaitu](contacts/chaitu.md) (▶) · [wiktor](contacts/wiktor.md) (▶, =Sekhmet) · [amalio](contacts/amalio.md) (▶) · [adkhaan-scammer](contacts/adkhaan-scammer.md) (do-not-engage)

## Reports — [reports/](reports/)
- Dated delta reports, newest first by filename (`reports/YYYY-MM-DD-HHMM.md`).
