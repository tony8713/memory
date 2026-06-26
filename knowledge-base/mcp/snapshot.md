# MCP: Snapshot

**Server:** `claude.ai Snapshot` (`mcp__claude_ai_Snapshot__*`).
**Purpose:** Snapshot governance — read spaces/proposals/votes/profiles via GraphQL, and (write) cast votes, create proposals, follow spaces.
**Auth note:** The user's address is auto-bound as `$user` on every `snapshot-query` — declare `query Foo($user: String!)` and reference it; do NOT pass `user` in `variables` (it's overwritten). Writes are signed by a delegated `alias` wallet that must be `authorized`.

## Real captured context (`snapshot-whoami`, 2026-06-27)
- **address** (acting on behalf of): `0x257c61Ba94D119e7625CA752297eF9e6913E88Ef`
- **alias** (delegated signer): `0x71e1C11879C508C96a0E72FbA360f5f0Bc508146`
- **authorized:** `true` (writes would currently succeed — but loop never invokes them)
- profile: name/about/avatar all null
- links.profile: snapshot.box/#/profile/0x257c61Ba94…E88Ef

`snapshot-query` sample (spaces first:3 + follows + user):
- spaces: `leads.ssvnetwork.eth` (Leads), `grovefinance.eth` (Grove), `evena.eth` (EVE N²/A DAO)
- follows: `[]` (this user follows no spaces)
- user.name: null
- Shape confirmed: space `id` is a slug (e.g. `ens.eth`), never the display name; timestamps are unix seconds UTC.

## Tool inventory
READ-safe:
- `snapshot-whoami` — connected identity, alias, authorized flag, profile. **READ.** (called above)
- `snapshot-query` — arbitrary GraphQL against the Snapshot API. **READ.** ($user auto-bound). (called above)
- `snapshot-schema` — schema introspection, only when a query errors on an unknown field. **READ.**

WRITE / mutating (documented — NEVER invoked in the loop):
- `snapshot-vote` — cast a vote (`space`, `proposal`, `choice`). Re-calling on the same proposal REPLACES the prior vote (how to change a vote). **WRITE.**
- `snapshot-propose` — create a proposal (only `space`, `title`, `body` required; type/choices/period/privacy derived from the space; snapshot block set to current chain head). **WRITE.**
- `snapshot-follow` — follow/unfollow a space (`action: "follow"` default / `"unfollow"`). Following an already-followed space errors; same for unfollowing one not followed. **WRITE.**

## Common query patterns (from server instructions)
- Find space by name: `spaces(where: { search: "<name>" })`
- Proposals: `proposals(where: { title_contains })` / `proposals(where: { space_in, state: "active" })`
- Voting power: `vp(voter: $user, space, proposal)` — evaluated at `proposal.snapshot` (a block), not now
- Followed spaces: `follows(where: { follower: $user })`
- Pagination caps: `first` ≤ 1000; `skip` ≤ 5000 on votes/proposals — cursor-paginate (`created_lt`) past 5000.

## Notes
- Auth/availability: available in this headless worker (whoami + query both succeeded). $user-bound address public; no secrets.
