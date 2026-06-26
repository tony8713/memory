# Repos & infrastructure

Repos Tony works in, local paths, and the infrastructure around them. For SX vote-pipeline architecture, see `skills/diagnose-sx-vote-pipeline.md` + `memory/snapshot-x-architecture.md` rather than duplicating here.

## Repos
### bonustrack/stage — the Stage app
- Cross-platform app: **Expo / React Native + Vue**. Native deep-link scheme **`stage://`** (Less wants native `stage://` links, not the web deploy-preview, for PR testing).
- UI deploy previews: **`deploy-preview-<N>--stage-livenet.netlify.app`**.
- **PR #636** — ChatKit refactor (Kit JSON renderer + shared view builders, RN + Vue, ~100 files). Build passes; CI gate red on lint/typecheck/knip/madge/test → not merge-ready. Testable via Expo preview + `stage://` deep links.

### snapshot-labs/sx-monorepo — Snapshot X
- `apps/ui` — the SX web UI (Netlify deploy previews `deploy-preview-<N>--snapshot-livenet.netlify.app`; channels deep link `…/#/channels`).
- `apps/mana` — the **relayer** (relays L1→L2 / signed actions). DB is **PostgreSQL on PlanetScale (psdb)** — PG 17.10, Patroni HA, direct `:5432`, no PgBouncer. Logs in Better Stack (source `1474369`, table `mana_pino`).
- `packages/sx.js` — SX client/SDK library.
- `apps/api` — SX API.
- Open/recent PRs: **#2181** (backend) / **#2185** (UI split off #2181) / **#2183** (`fix/ledger-vote-via-tx`, verified healthy) / **#2189** (lost-write DETECTION layer for #2186 — `.merge()` upsert + read-back verify; not the recovery fix). See `STATUS.md` for the #2186 lost-write root cause.

### snapshot-labs/checkpoint — the indexer
- Indexes on-chain events for SX. **PR #394** — HyperSync rate limiter.
- Indexers run across **droplets** (e.g. eth indexer on droplet 2 had a real 26h+ outage Jun 24). Monitored via Better Stack "Silent indexers" alert + SX Indexers dashboard (`1041825`).

### nickai-app-fork
- A fork repo in scope. **(unverified — purpose not documented here; confirm before relying on it.)**

## Local paths
- `~/work` — cloned repos live here.
- `~/.metro` — Metro daemon config, "trains", `.env`.
- `~/.config/metro` — additional Metro config.

## Infrastructure
- **mana relayer** uses a **per-space Starknet account** (STRK-funded) to relay.
- **Better Stack** monitors the SX Indexers (dashboard `1041825`); the "Silent indexers" alert card can read **0 even during a real outage** (the silent-count query decays 1→0), so 0 ≠ healthy.
- **Netlify** deploy previews for both `apps/ui` (snapshot) and Stage.
- mana relayer DB diagnosis (#2186) used a **read-only grant** to the psdb Postgres — no credentials stored in this repo. Per-statement errors need **PlanetScale Query Insights** (dashboard); `pg_stat_statements` / `pgaudit` are not installed.
