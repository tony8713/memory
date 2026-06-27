# Skill: diagnose an SX Starknet vote that didn't land

Trace end-to-end; confirm each hop before blaming the next.

1. **Identify the authenticator route** (EthSig vs EthTx vs StarkTx) for the user. EVM users default to EthSig; hardware wallets need EthTx. See `memory/snapshot-x-architecture.md`.
2. **L1 side**: confirm the `commit()` tx is mined on L1.
3. **L1→L2 message**: confirm consumed. On the EthTx authenticator (`0x63c89d1c...b442b0ea0`) check `_commits[hash][voter]`. **A SUCCESSFUL vote CLEARS `_commits` back to `0x0`** — the authenticator deletes the commit on consumption. So: `0x1` = committed-but-never-voted (stuck); `0x0` AFTER a confirmed index = success (NOT "never registered"). Do not misread a cleared `0x0` on an indexed vote as a failure.
4. **mana registration**: check for a `registered_transactions` row. **If the vote modal showed SUCCESS, the row WAS created** — the modal only flips to `success` after `await registerTransaction` resolves (failure re-throws → `fail`; `useActions.ts:82`, `mana.ts:15-33`, `rpc.ts:92-94`). So a success modal rules out the "lost POST" theory.
5. **L2 indexing**: confirm the L2 `authenticate` / vote indexed.

## #2186 FINAL root cause — a lost/non-durable DB write masked by silent success (PR #2189)
Three theories were tried; the first two are DISPROVEN — keep them documented so they aren't re-proposed:
- **NOT the mana 24h sweep** (`markOldTransactionsAsProcessed`). DB showed recent MAINNET rows succeeded; only old SEPOLIA rows failed — failure does not track the 24h boundary.
- **NOT hash zero-padding.** pedersen `getStorageVarAddress` is padding-agnostic: `addr('0x291…') === addr('0x0291…')` (same storage slot).

**Actual cause:** for the missing votes, the `registerTransaction` INSERT never persisted, yet mana logged "Registering transaction" with no error and returned success.
- Proof: Better Stack `mana` logs show the POST arrived; read-only DB shows no row; the decisive contrast is the processing-loop count — the vote that LANDED (row id 38) flips count `0→1` then "broadcasted successfully", while the missing votes NEVER reached `count:1` → the write never committed.
- Mechanism: a lost/non-durable write at the PlanetScale `psdb.cloud` Postgres gateway, MASKED by (1) `db.ts` `registerTransaction` `.onConflict().ignore()` swallowing the no-op, and (2) `rpc.ts` returning `rpcSuccess(true)` unconditionally with no read-back. UI sees ✅, relayer never sees the row, commit stuck at `_commits=0x1`.

**Log signature to look for:** "Registering transaction" present, no error, but the processing loop for that hash never reaches `count:1`.

**Fix (PR #2189, `fix/mana-register-transaction-verify-write`):**
- `db.ts`: `.onConflict(['sender','hash']).merge({ updated_at }).returning('id')` → return row/null instead of ignore.
- `rpc.ts`: read the row back via `getDataByMessageHash` after insert; `rpcError(500)` if absent, else `rpcSuccess`.

### #2189 CLOSED (2026-06-27, Wan's call) — a try/catch CANNOT catch this; logs are exhausted
From the review: **master already has a handler-level try/catch** — any THROWN error in the register path is already caught + 500'd. So a try/catch added in #2189 is **redundant** (catches nothing new). The lost write is a **SILENT 0-row no-op, not a throw**: `.onConflict(['sender','hash']).ignore()` returns 0 rows and does NOT throw on a conflict / non-durable psdb write. **No try/catch (handler-level or added) can catch a no-throw.** Also: the existing **"Registering transaction" log already carries sender + hash**, so it gives the diagnostic anchor — no new logging is needed. Wan therefore **CLOSED #2189** (branch `fix/mana-register-transaction-verify-write` kept for reference).

Only three things can surface the silent no-op (none is a code-log change):
1. **PlanetScale Query Insights** (dashboard) — the real per-statement behavior / WHY (source of truth; `pg_stat_statements`/`pgaudit` not installed → not reachable via SQL).
2. **rowCount / `.returning('id')` inspection** — act on 0 affected rows after the insert.
3. **Drop `.ignore()`** (→ `.merge()`) so a real conflict/error can surface.

**Logs exhausted → Query Insights is the source of truth.** A conclusive 2026-06-27 dig of the two missing-vote timestamps (15:11:13Z & 19:48:55Z) in Better Stack `mana` (`mana_pino`) found **NOTHING anomalous**: register logged, **pid 19 throughout** (no restart), **no "Failed to register transaction"**, no connection/pool/timeout events, processing loop uninterrupted — **yet no row.** Only unrelated errors in the window (3095× "Failed to process proposal" starknet RpcError/herodotus from 19:58; some "timestamp in the future"; "Failed to get data by message hash" reads). The app layer is clean → the loss is at the **DB-gateway (psdb) layer**, invisible to app logs → **PlanetScale Query Insights for those two timestamps is the only remaining source.**

**Postgres statement logging WASN'T capturing INSERTs (2026-06-27):** Wan supplied two logs to chase per-statement behavior; both were the wrong type — (1) a PlanetScale infra slice (Patroni/pgbouncer chatter, ~78-sec window, no query data), and (2) a Better Stack Postgres statement-log filtered to `registered_transactions` over 06-20→06-27 showing **ZERO INSERTs** (only a `pg_stat_statements` meta-query + one `select … where processed` read). So `log_statement` is **not** `mod`/`all` → statement logging never recorded the INSERTs, and the two past missing votes are **unknowable from logs**. **Forward fix (no app change):** set `log_statement='mod'` (or `log_min_duration_statement=0`) on the mana Postgres so future INSERTs + their errors get logged, then wait for the next occurrence — OR use PlanetScale **Query Insights** (dashboard). AWAITING Wan's decision on flipping `log_statement`.

**Duplicate-ignore vs genuine loss (key distinction):** a `(sender, hash)` for which **no row exists** CANNOT be a `.onConflict().ignore()` duplicate-drop — a duplicate-ignore presupposes the row already exists. So "no row + register logged" is **genuine loss**, not the upsert dropping a real duplicate. Don't conflate the two when triaging.

## On-disk DB confirmation (read-only dig — confirms allocated-but-uncommitted inserts)
Verified the lost-write mechanism directly in the mana Postgres (PG 17.10, Patroni HA). How to dig (all read-only; never record the connection string):
- **Check id-sequence GAPS.** Persistent missing ids in a contiguous range = `nextval` consumed but the row never committed (a sequence advances on `nextval` regardless of COMMIT). Found 7 gaps in ids 1–40 (14,15,20,24,27,29,39); id 39 sits between id 38 and id 40 → an allocated-but-uncommitted insert. **Hard evidence of recurring lost inserts.**
- **Rule out server-side rollback/deadlock:** `pg_stat_database` → `xact_rollback` (was 1 lifetime), `deadlocks`/`conflicts` (0). If ~0, the server isn't rolling these back → loss is APP-SIDE before COMMIT.
- **Rule out pooler/idle-timeout:** check `idle_in_transaction_session_timeout`, `idle_session_timeout`, `statement_timeout` (all 0 here), direct :5432, no PgBouncer. If all 0/direct, a pooler isn't killing the txn.
- **Per-statement error:** `pg_stat_statements`/`pgaudit` are available-but-not-installed and there's no audit table → the exact failing statement needs **PlanetScale Query Insights (dashboard)**, not SQL.

## The real-fix ladder: detect → retry → self-heal → durability
#2189 makes the failure **DETECTABLE, not RECOVERED.** Real fixes (recommend self-heal):
1. **Detect** — #2189 read-back + upsert (done; just surfaces the error).
2. **Retry** — client/UI retry on the `rpcError`; safe + idempotent now the write is a `.merge()` upsert.
3. **Self-heal (recommended real fix)** — relayer watches `StarknetCommit` `CommitAdded` / scans `_commits` and self-registers committed-but-unregistered votes independent of the client POST; recovers even orphaned commits.
4. **Durability** — knex pool `afterCreate` ping + txn-wrap the insert so a dropped psdb connection rejects instead of silently no-op'ing.

**Gotcha:** an orphaned-but-valid commit can't be cheaply re-triggered — reproducing the commit hash needs the original `metadataUri`/reason text, not recoverable from chain.

**Signing errors:** for "invalid signature length" / Ledger rejections, get the **full error object** before diagnosing — the throw is often in MetaMask `KeyringController.signTypedMessage`, upstream of sx.js.
