# Infra & monitoring

## Indexers
- Indexers run across droplets. The **eth indexer on droplet 2** went genuinely silent ~06:50 UTC 2026-06-24 for 26+ hrs (a real outage, NOT a false positive).

## Better Stack "Silent indexers" alert
- The alert card can show **0** even during a real outage: once a chain fully stops emitting, the silent-count query can decay 1→0. So a 0 on that card does not mean "healthy".
- Proposed improvement: add a `silent_chains` name column to the alert so it names which chain went silent instead of just a count.

## Deploy previews
- `apps/ui` PRs get Netlify deploy previews: `deploy-preview-<PR>--stage-livenet.netlify.app`.
- Channels view deep link: `…/#/channels`.
- TS type errors (e.g. TS2677 type-guard bugs) can fail UI deploy previews silently from a backend PR — check the preview build when splitting.

## ChatKit / bonustrack/stage
- PR **#636** "Kit JSON renderer + shared view builders (RN + Vue)" (~100 files). Build passes but the CI gate is red on lint/typecheck/knip/madge/test.
