# Metro reliability hardening plan

Synthesized from the 2026-06-28/30 incident session (cloud `metro` Fly app `185d62eb399538`).
Each failure mode: problem → root cause → fix → risk → priority. No secrets in this file.

Repo references are to `~/work/metro-protocol` (the cloud metro server) unless noted.

---

## P0 — fix immediately

### 1. MCP stream instability / zombie after deploy or idle
**Problem.** Within minutes of each manual `/mcp` reconnect the orchestrator's connection to the
cloud `metro` MCP server wedges: `read` times out at 300s constantly; `react`/`reply` intermittently
time out client-side but the server-side send often still lands; inbound SSE `<channel>` events stop;
then a full disconnect. A `/mcp` reconnect helps briefly, then it re-wedges. Started right after the
18:33Z Fly deploy bounced the machine.

**Root cause.** The HTTP layer (`apps/mcp/src/mcp/index.ts`) multiplexes **all** requests and **all
clients onto a single shared `StreamableHTTPServerTransport`** (one global `let transport`), instead
of the SDK's intended per-session map keyed by `mcp-session-id`. On every request `syncSession()`
calls `rebind()` (which does `transport.close()` + reconnect + new random session id) whenever the
request is an `initialize` OR the presented session id differs from the current one. Consequences:
- A long `read` response is being written when any other request arrives (the standalone GET SSE
  re-establish, another POST, or a parallel `initialize`). `rebind()` closes the transport
  **mid-response** → the read stream is torn down → the client hangs to its 300s timeout. Small
  `react`/`reply` finish fast enough to usually dodge the race, and even when their client leg is cut,
  the XMTP side effect already executed — exactly the observed "times out but still delivers."
- Server→client notifications (`notifications/claude/channel` from `InboundRelay`, and keepalive
  `mcp.ping()`) flow over the standalone GET SSE stream. After a `rebind()` the server session id
  changes to a fresh UUID; the client's existing GET stream is orphaned and new notifications have
  nowhere to go → **inbound events silently stop**.
- **Two concurrent clients are fatal here.** Any second MCP client (e.g. the local
  `box.metro.dispatcher`, or an overlapping reconnect) presents a different session id on every call,
  so the two clients mutually `rebind()` each other's transport on every request → permanent thrash =
  "re-wedges within minutes." The post-deploy reconnect storm (machine bounce → client retries →
  repeated `initialize` → repeated rebind) is what kicked it off at 18:33Z.

Fly's ~60s idle proxy timeout is **not** the primary cause: `Keepalive` pings every 25s
(`KEEPALIVE_INTERVAL_MS = 25_000`, `apps/mcp/src/mcp/keepalive.ts`), under the 60s window. The
`fly-proxy ... timed out ... request.url="/mcp?token=…"` log lines are a **symptom** of the orphaned /
half-open GET stream left behind by a rebind, not the trigger. (Keepalive only helps a stream that is
still attached; a ping on an orphaned stream just logs "keepalive ping failed" and recovers nothing.)

**Fix.**
1. Adopt the SDK's per-session model: keep `Map<sessionId, StreamableHTTPServerTransport>`; create a
   transport only on `initialize`; route by the `mcp-session-id` header; on an unknown/mismatched
   session id return HTTP 404 (per the Streamable HTTP spec) so the client cleanly re-initializes
   **instead of stealing/closing the live transport.** Never `transport.close()` because a different
   session id appeared, and never close while a request is in flight. This single change removes the
   read-timeout race, the orphaned-SSE problem, and the two-client thrash.
2. Stop poking SDK internals (`_webStandardTransport.sessionId` / `_initialized`) to "adopt" arbitrary
   ids — that hack is what makes the single-transport collapse seem to work and hides the bug.
3. Keep the 25s keepalive (it is correctly under Fly's idle timeout) but it must operate per session.
4. Client-side: rely on Claude Code auto-reconnect; pair with backfill (see #7) so a reconnect does
   not lose the messages that arrived during the gap.

**Risk.** Touches the core transport routing; a wrong session-map lifecycle could leak transports
(memory) or drop a legitimate client. Mitigate with a session idle-expiry sweep and a test that opens
two clients + a long `read` concurrently and asserts neither wedges. Medium implementation risk, high
payoff. **P0.**

### 3. Competing clients saturate the XMTP inbox
**Problem.** The local `box.metro.dispatcher` ran the same trains as the cloud machine, producing a
second live XMTP client per inbox.

**Root cause.** Two writers on one XMTP inbox → installation-cap saturation and a shared-IP rate
bucket that blocked **all** sends. It also feeds failure mode #1 (two MCP clients fighting one
transport). The Fly volume single-attach rule enforces one cloud writer, but nothing stops a laptop
dispatcher from also booting the same identities.

**Fix.** Retire/disable the local `box.metro.dispatcher` permanently; guarantee exactly one client per
inbox (cloud machine only). Installations were already pruned to ~1/inbox this session — keep them
pruned. Document "one client per inbox" as an invariant.

**Risk.** Low. Losing the local dispatcher removes a manual fallback; acceptable given it is the
direct cause of send outages. **P0.**

---

## P1 — soon

### 2. Deploy drift: uncommitted/untracked code not in the deployed image
**Problem.** The XMTP loader patch that lets the cloud `tony` identity boot was **uncommitted local
code**; the deployed image silently lacked it → cloud `tony` never started. As of this session the
working tree still shows `M packages/xmtp/src/accounts.ts` plus **untracked** `packages/mcp/` and
`packages/metro/` — i.e. live code that exists only on the laptop.

**Root cause.** Deploys build from a tree that can diverge from `git HEAD`, and nothing fails the
deploy when the working tree is dirty or has untracked source. The patch lived only in the working
copy.

**Fix.** Commit the loader patch and the untracked `packages/*`. Add a **preflight that refuses to
deploy when `git status` is not clean** (no modified/untracked tracked-relevant files) and ideally
stamps the deployed image with the commit SHA, surfaced at `/health`, so deployed == committed is
verifiable. Build from a clean checkout in CI rather than the laptop tree.

**Risk.** Low. The preflight could block an urgent hotfix — allow an explicit override flag for
emergencies. **P1.**

### 5. No inbound liveness monitoring / alerting
**Problem.** Inbound being dead is only discovered when a human (Less) notices nothing is getting
through.

**Root cause.** `/health` reports process liveness only; there is no per-station inbound heartbeat and
no alert when a station stops delivering events.

**Fix.** Emit a per-station inbound heartbeat (last-event timestamp per train/line) and a healthcheck
that goes unhealthy when any expected station has been silent beyond a threshold; wire an alert
(Better Stack uptime/heartbeat monitor) so a stalled station pages instead of waiting for Less.

**Risk.** Low. Tune thresholds to avoid false alarms on quiet channels. **P1.**

### 7. Reconnect loses messages (no backfill)
**Problem.** Events that arrive during a stream gap (rebind, reconnect, deploy) are dropped — the
reconnected client never sees them.

**Root cause.** `makeDedupSeq()` in `apps/mcp/src/daemon/http.ts` logs "dedup+seq: live-from-boot (no
persisted seed)" — there is no persisted cursor and no replay. Notifications are fire-and-forget over
the live SSE stream; if the stream is down they are gone.

**Fix.** Persist a per-line seed/cursor (the `seq` already minted per line) and implement Streamable
HTTP **resumability**: supply an `eventStore` to `StreamableHTTPServerTransport` (supported in
`@modelcontextprotocol/sdk` ^1.12) so the client replays from `Last-Event-ID` on reconnect, and/or a
small bounded on-disk ring buffer keyed by line+seq that the client backfills on reconnect. Pairs with
the #1 fix.

**Risk.** Medium — must bound storage and de-dup on replay to avoid double-processing (the relay
already has a 30s dedupe window; extend it to cover replayed ids). **P1.**

---

## P2 — follow-ups

### 4. Fragile account config (`DERIVE_COUNT`)
**Problem.** `DERIVE_COUNT=1` derives only index x0 and silently drops x1. Harmless this time (Less
uses x0) but the pattern can drop a live inbound identity without warning.

**Root cause.** `packages/xmtp/src/accounts.ts` derives indices `0..N-1` from a single count
(`DERIVE_COUNT`, default 1); flipping the count silently changes which inboxes exist.

**Fix.** Pin an explicit account/identity set (named indices or addresses) rather than a count, so a
count change can never drop a live inbound identity. Fail loudly if a configured-active identity is
not derived.

**Risk.** Low. **P2.**

### 6. telegram-user inbound never implemented
**Problem.** There is no MTProto/user-mode Telegram train — only bot tokens (t0/t1/t2). The
`TELEGRAM_USER_*` secrets are set but no code consumes them, so user-account Telegram inbound does not
exist.

**Root cause.** Feature was never built; secrets were provisioned ahead of code.

**Fix.** Build a `telegram-user` train using a gramjs `StringSession` (user-mode MTProto), consuming
the `TELEGRAM_USER_*` config. Scope as a standalone follow-up project, not part of this incident fix.

**Risk.** Medium — user-account automation has stricter ToS/rate considerations than bot tokens.
**P2.**

---

## Priority summary
- **P0:** #1 MCP single-transport/rebind thrash (per-session transports, never close on mismatch);
  #3 retire local dispatcher (one client per inbox).
- **P1:** #2 commit patch + clean-tree deploy preflight; #5 per-station inbound heartbeat + alert;
  #7 persisted cursor + resumable backfill.
- **P2:** #4 pin explicit identity set; #6 build telegram-user (gramjs) train.
