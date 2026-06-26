# Skill: Push a Safe-signed Snapshot vote to the hub

Use when Less (or another trusted member) asks to submit a Snapshot vote that was
**signed by a Safe** (multisig) and needs to be relayed to the Snapshot sequencer.
This is the EIP-1271 / off-chain-message path — the Safe never sends an on-chain tx;
owners sign an EIP-712 message and the aggregated signature is submitted to Snapshot.

## Inputs you need
- The Safe address (e.g. `0xCa1cc2DB71fBcA354ddc59507Eb7256112a3BFFb`, eth mainnet).
- The Safe message hash (the `messageHash` of the EIP-712 snapshot-vote message).

## Steps

### 1. Fetch the Safe message
```
GET https://safe-transaction-mainnet.safe.global/api/v1/messages/<messageHash>/
```
(Use the correct Safe Transaction Service host for the Safe's chain.)

### 2. Verify it is FULLY signed (guardrail — do NOT submit otherwise)
From the response, require BOTH:
- `confirmationsSubmitted >= confirmationsRequired` (e.g. 2/3 threshold met), and
- `preparedSignature` is non-null.

`preparedSignature` is the aggregated EIP-1271 signature blob the hub will verify
against the Safe contract. If it's null / threshold not met, STOP and tell the
requester it isn't fully signed yet. Submitting early just gets rejected by the hub.

### 3. Parse the EIP-712 snapshot vote
The Safe message `message` field is the EIP-712 typed-data object:
`{ domain, types, message }` for a Snapshot `Vote`. The `message` carries:
`space`, `proposal`, `choice`, `reason`, `app`, `metadata`, `from`, `timestamp`.

### 4. POST to the sequencer
```
POST https://seq.snapshot.org/
Content-Type: application/json

{
  "address": "<Safe address>",
  "sig": "<preparedSignature>",
  "data": { "domain": {...}, "types": {...}, "message": {...} }
}
```

**GOTCHA — numbers, not strings:** the sequencer rejects the envelope if
`message.choice` or `message.timestamp` are JSON **strings**. They MUST be JSON
**numbers**. The EIP-1271 signature is still valid (the same uint hash is recovered
from a number or its string form), so do NOT re-sign — just coerce the types to
numbers in the JSON you POST.

A success response returns `{ id, ipfs, ... }` — the vote id (e.g.
`0x08b08bb2…`) and the IPFS CID.

### 5. Verify it landed on the hub
Query hub.snapshot.org/graphql and confirm the vote exists:
```graphql
query { votes(where: { voter: "<Safe address>", proposal: "<proposalId>" }) {
  id voter choice vp created reason } }
```
Confirm `choice` / `vp` match. Then report the proposal URL to the requester:
`snapshot.org/#/<space>/proposal/<proposalId>`.

## Notes
- vp is evaluated at `proposal.snapshot` (a block), not now — the Safe's power is
  whatever it held at that block.
- Re-submitting for the same proposal replaces the prior vote (same as any voter).
- Safe addresses, vote ids, IPFS hashes, and proposal ids are all public — fine to
  log/report. preparedSignature blobs are public too (already on the Safe service).
