# Less

- Slug: less
- Trust: TRUSTED (one of the 5)
- Reply-prefix: ■
- Role: Team lead / owner. Directs most of Tony's work; owner of this memory repo.

## Platform IDs
- Discord: `bonustrack_` (global_name "less", id 238307675501232128)
- XMTP: verified, address tail …ef8b (line metro://xmtp/x0/85e076a16839fc6431056bcdd15f68b3)
- Telegram: @bonustrack (user 25220238), stations: telegram bot `t0` + telegram-user station

## Key channels (see `knowledge-base/channels/README.md`)
- Discord **#api** `1030772407226601522` (k8s migration, Checkpoint #390/#394, #2125 split) · **interns** `1478881436168880218` · **gtm** `1448596560010150009` · his **test/DM channel** `1504226489359401221`.
- Telegram `t0` (user 25220238) + telegram-user station; XMTP `metro://xmtp/x0/85e076a16839fc6431056bcdd15f68b3`.

## Context / notable interactions
- Directs most work and sets Tony's conventions.
- Defined the trust + reply-prefix scheme (■ for Less, ▶ for the other trusted 4).
- Restored the Telegram user credentials (TG_USER) so the telegram-user station works.
- Owner of the memory repo and the contacts-tracking task.
- 2026-06-26: Asked for PR #636 deep links — wanted the NATIVE `stage://` scheme (not the web deploy-preview). Shared a Stage History screenshot showing 20,000 USDC received / Success (Jun 26 1:26 AM); used as a healthy-UI data point.
- 2026-06-26: Granted tony READ-ONLY access to the mana DB (no credentials stored in repo) — used to disprove the 24h-sweep theory and nail the lost-write root cause for #2186. Ran connectivity sweeps and flagged missing Metro messages.
- 2026-06-27: Asked tony to **split UI from API in sx-monorepo PR #2125** (API stays, UI → stacked branch). Also relayed the `sekhmet-review` of checkpoint#390 to Less (utACK/COMMENT; nested-query plural blocker + computed-where prefix finding). Awaiting his/Sekhmet's go to execute the #2125 split.
- 2026-06-26: Requested pushing a Safe-signed Snapshot vote to the hub — Safe `0xCa1cc2DB71fBcA354ddc59507Eb7256112a3BFFb`, space gnosis.eth, proposal GIP-151, choice For. Routes Safe / governance actions through Metro (signs in the Safe, has Metro relay the signed message to the Snapshot sequencer). See skills/push-safe-signed-snapshot-vote.md.
