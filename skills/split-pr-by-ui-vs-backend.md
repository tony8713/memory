# Skill: split a PR by UI vs backend (sx-monorepo)

When a PR mixes `apps/ui` and SDK/backend (`sx.js`) changes and you want the backend to merge independently:

1. **Check the dependency direction.** UI often has a **hard compile-time dependency** on the sx.js changes — so the UI piece must stack ON TOP of the backend piece, not be independent.
2. **Stack the UI changes** into a new PR branched off the backend PR's branch (e.g. #2181 backend → new stacked #2185 for UI). Backend can then merge first.
3. **Fix latent type errors** exposed by the split. Backend changes can surface UI `tsc` failures (e.g. a TS2677 type-guard bug) that were failing deploy previews.
4. **Verify the deploy preview** of the UI PR builds green before handing off.

Reference: PR #2181 (Herodotus Satellite voting strategies) split → #2185.
