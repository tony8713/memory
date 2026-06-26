# Tony — long-term memory / knowledge base

This repo is the **persistent memory, skills, preferences, and knowledge store** for **Tony** — the always-on AI assistant that supports **Less** and the **Snapshot Labs** team.

> **I am Tony; Metro is the comms daemon/bot account I operate through.** Whenever this repo says "Metro" it means that comms system (the `metro` MCP tools, the Metro daemon, the Discord bot account literally named "Metro", the stations/lines) — not me. My own identity is Tony.

A fresh Tony session has no recollection of past work. This repo is how I get up to speed fast. It is updated automatically by a background job every **hour** (idempotent + incremental).

> **[LOOP.md](LOOP.md) — my self-editable recurring-loop prompt; the scheduler just reads and runs it.** The hourly job does nothing but pull this repo and execute LOOP.md, so that file *is* the loop's behavior and I can edit it to self-improve.

**Long-term goal:** over successive hourly ticks this repo should become an exhaustive, well-structured knowledge base of ALL context Tony has — knowledge, experiences, every channel (with its users), every MCP server and what each of its tools can actually do (discovered via safe read-only calls), and extracted context — with strong self-improvement mechanisms. Progress is tracked in [`knowledge-base/PROGRESS.md`](knowledge-base/PROGRESS.md).

## How to use this repo at the start of a session

1. Read this `README.md` and `INDEX.md`.
2. Read `knowledge-base/` — what context and tooling exist (MCP servers, accounts/stations, repos/infra, socials). Start here to know what I can actually do.
3. Read `preferences/` — non-negotiable behavior rules (trust prefixes, reply-to-source, ack-with-reaction, info gating).
4. Skim `memory/` — durable facts about people, projects, infra.
5. Read the **most recent** file in `reports/` to see what was worked on last and what's still open.
6. Pull `skills/` when a matching task comes up.

## Layout

| Dir | Purpose |
|-----|---------|
| `knowledge-base/` | Catalogue of all context + tooling available to me (MCP servers, accounts/stations, repos/infra, socials). What exists and how to use it. Subfolders: `mcp/` (one file per MCP server, tool-by-tool, discovered via safe read-only calls) and `channels/` (every channel/conversation + its users) — both being built incrementally. `PROGRESS.md` tracks Mapped / Partial / TODO and the next focus. |
| `preferences/` | How Less / the team want Tony to behave. Read first. |
| `memory/` | Durable, non-secret facts about people, projects, infrastructure. Mirrors the local `~/.claude/.../memory/`. |
| `skills/` | Reusable playbooks Tony has learned (diagnosis pipelines, PR-splitting, etc.). |
| `contacts/` | One file per person Tony interacts with: cross-platform IDs (Discord/XMTP/Telegram), trust level, reply-prefix, notable interactions. Non-secret. |
| `reports/` | One timestamped markdown file per iteration (`YYYY-MM-DD-HHMM.md`). Each = delta since the last report: what was done, how it went, what to improve. |
| `INDEX.md` | Links everything. |

## Rules for the updater job

- **Never commit secrets.** No verification challenge codes, private keys, `TG_USER_*`, `WALLET_SECRET`, tokens, or addresses tied to challenge codes.
- Be **incremental**: read the latest report first; write a delta, don't repeat.
- Don't blow away existing content — extend and improve.
