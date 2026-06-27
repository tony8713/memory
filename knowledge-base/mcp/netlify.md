# MCP: Netlify

**Server:** `claude.ai Netlify` (`mcp__claude_ai_Netlify__*`).
**Purpose:** Netlify hosting for Snapshot/Stage frontends — read user/teams/projects (sites) + deploy state; (write/updater) mutate projects, deploys, extensions.
**Auth note:** Logged in as **Tony Luciano** (`tony@bonustrack.co`, github `tony8713`). Available in this headless worker (reader calls succeeded). No tokens exposed.

## Real captured context (2026-06-27)
### Teams (`netlify-team-services-reader get-teams`)
- **`tony8713`** ("tony", Free) — personal team, role **Owner**, 1 member, 1 site.
- **`stagelabs`** ("stage", Pro) — role **Developer**, 2 members → Stage frontends.
- **`snapshotlabs`** ("snapshot", Pro) — role **Reviewer**, 5 members → Snapshot frontends.

### snapshotlabs sites (`get-projects teamSlug=snapshotlabs`) — primary URLs
| Project | Primary URL |
|---|---|
| `snapshot-livenet` | **snapshot.box** (prod app) |
| `snapshot-testnet` | testnet.snapshot.box |
| `snapshot-v1` | v1.snapshot.box (legacy) |
| `snapshot-docs` | snapshot-docs.netlify.app |
| `snapshot-landing` | snapshot-landing.netlify.app |
| `snapshot-storybook` | storybook.snapshot.box |
| `checkpoint-ui` | checkpoint.snapshot.box |
| `checkpoint-console` | console.checkpoint.snapshot.box |
| `brovider` | brovider.netlify.app |
| `envelop-ui` | envelop.fyi |
| `snapshot-whitelabel` | snapshot.xmaquina.io (whitelabel deploy) |

### stagelabs sites (`get-projects teamSlug=stagelabs`)
| Project | Primary URL |
|---|---|
| `stage-livenet` | **stage.box** (Stage prod) |
| `invertronix` | invertronix.netlify.app |

All sampled deploys were `state: ready` / `current`. (Site IDs available via the reader if needed; omitted here.)

## Tool inventory
READ-safe (reader services; captured where noted):
- `netlify-user-services-reader` → `get-user`. **READ.** (captured)
- `netlify-team-services-reader` → `get-teams`, `get-team`. **READ.** (captured get-teams)
- `netlify-project-services-reader` → `get-projects` (by team/name), `get-project` (by siteId), `get-forms-for-project`. **READ.** (captured get-projects)
- `netlify-extension-services-reader` → read installed extensions. **READ.**
- `netlify-deploy-services-reader` → read deploy details/logs. **READ.**
- `get-netlify-coding-context` — Netlify dev/coding guidance (meta). **READ.**

WRITE / mutating (documented — NEVER invoked):
- `netlify-deploy-services-updater` — trigger/modify deploys. **WRITE.**
- `netlify-project-services-updater` — create/modify projects/settings. **WRITE.**
- `netlify-extension-services-updater` — install/configure extensions. **WRITE.**

## Notes
- This is the canonical map of which domain → which Netlify project for SX + Stage.
- Tony is **Reviewer** on snapshotlabs (read/review, not deploy) and **Developer** on stagelabs.
