# MCP: OAuth-gated + Slash + Browserbase

Servers that are auth-gated (only `authenticate`/`complete_authentication` exposed until the user completes OAuth in a browser) or otherwise unsafe to exercise read-only in a headless worker. Documented from schema/description only — **no auth flows started, no sessions opened, no data-returning calls made.** Each: verify availability in the main (interactive) session.

## OAuth-gated (auth-gated; real tools appear only after OAuth)
All three expose exactly two tools (`authenticate`, `complete_authentication`); their real toolset is hidden until authorized. Do NOT call `authenticate` from a worker (no browser to complete the flow).

| Server | Proxy endpoint | Purpose | Verify in main session |
|---|---|---|---|
| `claude.ai Cloudflare_Developer_Platform` | `bindings.mcp.cloudflare.com/mcp` | Cloudflare developer platform (Workers/KV/D1/R2/bindings, DNS/zones). SX uses Cloudflare for DNS + some edge; high-value if wired to the snapshot.box zone. | Auth-gated; confirm scope + which account it lands on. |
| `claude.ai Calendly` | `mcp.calendly.com` | Scheduling — event types, scheduled events, invitees, availability. | Auth-gated; likely low priority. |
| `claude.ai Docusign` | `mcp.docusign.com/mcp` | e-signature — envelopes, templates, signing. Legal/contract surface. | Auth-gated; treat output as sensitive (legal docs). |

## Slash (`claude.ai Slash`) — REACHABLE, but financial/PII surface → schema-only
**Status: reachable** (`list_endpoints` returned 100+ endpoints) but it is a **business banking / spend-management API** (accounts, balances, cards, transactions, invoices, expense reports, contacts/customers). Treat ALL data-returning GETs as financial PII → NOT called this tick. Document from the endpoint catalog; in main session use deliberately and never log balances/card data.

Tools: `list_endpoints` (READ, safe — done), `get_endpoint_schema` (READ, safe — describe one endpoint), `call_api_endpoint` (proxies any REST endpoint; **R/W depends on the HTTP method** — GET=read but PII; POST/PUT/PATCH/DELETE=WRITE/money-moving). `call_api_endpoint` takes an `idempotencyKey` for mutations and a `legalEntityId`/`x-legal-entity` scope.

Endpoint groups (from `list_endpoints`):
- **Accounts/balances:** `/account`, `/account/{id}/balance`, `/virtual-account`, `/legal-entity`.
- **Transactions:** `/transaction`, `/transaction/aggregation`, `/transaction/{id}` (+ note/fee-details).
- **Money movement (WRITE/$$$):** `/transfer/virtual-account`, `/transfers/book-transfer`.
- **Cards (read + WRITE):** `/card`, `/card-group`, `/card-product`, spending-constraints + modifiers (PUT/PATCH = WRITE).
- **Invoicing/billing:** `/invoice` (+ series, settings, void/reconcile/reminder), `/contact`, `/customer`.
- **Expense reports:** `/expense-report` (+ approve/reject/receipt — approve initiates payout = WRITE/$$$).
- **Analytics:** `POST /analytics` (SQL over the user's data — read but returns financial data; needs `legalEntityId`).
- **Misc public:** `GET /tokens/prices` (real-time crypto prices, **no auth, safe**), OAuth/FDX/webhook/task plumbing.

> Only obviously-safe Slash call: `GET /tokens/prices` (public, no PII). Everything else is gated behind a legal entity and carries money/PII — don't poke for curiosity.

## `x-cloud` MCP — CONNECTED, READ/BOOKMARK-ONLY (corrected 2026-06-30)
**Status: LIVE but read-only.** The old dead `x` entry (static HTTP `http://127.0.0.1:8000/mcp`, no launcher, nothing on :8000) has been **REPLACED** by `x-cloud` → **`https://api.x.com/mcp`** (X's official hosted MCP, "xmcp", **bearer-auth** — token NOT recorded here, secret). `claude mcp list` shows it **✔ Connected**; `initialize` + `tools/list` work, exposing **24 tools**.

**Tools are READ + BOOKMARK only:** `search_posts` / `search_news` / `search_users`, get timelines / mentions / trends, `get_users_me`, and **create/delete bookmarks**. There is **NO `create_post`/tweet tool** — the token's access tier grants read + bookmark, no publish.

**Session caveat:** `x-cloud` tools are NOT loaded in the current loop session (entry added after session start) → needs a **reconnect / `/mcp` refresh** to surface them.

Consequence: **X-POSTING IS STILL UNAVAILABLE** (different reason than before — not "MCP down" but "no publish scope"). The BOOTSTRAP X auto-like step is also not directly serviceable (no like/auto-like tool; only bookmark). To enable posting: a **write-capable X token tier** (exposes create-post on `x-cloud`), **Zapier X write** OAuth (`mcp/zapier.md`), or **Typefully** (15-post/mo cap). See `../socials-and-external.md`.

## Browserbase (`claude.ai Browserbase`) — tools exposed, but stateful/agentic → not exercised
Cloud headless-browser automation. Tools: `start` (open/reuse a session), `navigate`, `act` (perform an action on the page), `extract` (pull data via instruction), `observe` (find actionable elements), `end` (close session). `act` mutates page state and `start` consumes a real cloud session — NOT invoked. Use only when a task genuinely needs to drive a browser (e.g. a site with no API); always `end` the session after.
