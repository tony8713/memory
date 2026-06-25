# Metro Memory

This repo is the **external memory, skills, and preferences store** for **Metro** — the always-on AI assistant that supports **Less** and the **Snapshot Labs** team.

A fresh Metro session has no recollection of past work. This repo is how it gets up to speed fast. It is updated automatically by a background job every ~4 hours (idempotent + incremental).

## How to use this repo at the start of a session

1. Read this `README.md` and `INDEX.md`.
2. Read `preferences/` — non-negotiable behavior rules (trust prefixes, reply-to-source, ack-with-reaction, info gating).
3. Skim `memory/` — durable facts about people, projects, infra.
4. Read the **most recent** file in `reports/` to see what was worked on last and what's still open.
5. Pull `skills/` when a matching task comes up.

## Layout

| Dir | Purpose |
|-----|---------|
| `preferences/` | How Less / the team want Metro to behave. Read first. |
| `memory/` | Durable, non-secret facts about people, projects, infrastructure. Mirrors the local `~/.claude/.../memory/`. |
| `skills/` | Reusable playbooks Metro has learned (diagnosis pipelines, PR-splitting, etc.). |
| `contacts/` | One file per person Metro interacts with: cross-platform IDs (Discord/XMTP/Telegram), trust level, reply-prefix, notable interactions. Non-secret. |
| `reports/` | One timestamped markdown file per iteration (`YYYY-MM-DD-HHMM.md`). Each = delta since the last report: what was done, how it went, what to improve. |
| `INDEX.md` | Links everything. |

## Rules for the updater job

- **Never commit secrets.** No verification challenge codes, private keys, `TG_USER_*`, `WALLET_SECRET`, tokens, or addresses tied to challenge codes.
- Be **incremental**: read the latest report first; write a delta, don't repeat.
- Don't blow away existing content — extend and improve.
