# Current-floor anchor reconciliation after supervised one-slice proof

Use this reference when a campaign has just completed a narrow supervised proof (for example one read, one source, one subject) and the user asks for a “super pass” on anchor docs, twin anchors, master batons, readiness docs, or scorecards.

## Pattern

1. Re-establish source truth before editing:
   - verify the current approved `origin/main` SHA;
   - create a clean detached worktree or exact-floor extraction;
   - avoid patching a stale/dirty pinned production clone directly.
2. Inventory active anchors, not every historical artifact:
   - continuity/master baton;
   - gate scorecard/blocker table;
   - artifact map/index;
   - adoption/readiness roadmap;
   - operator-facing twin anchor/vision doc;
   - any cross-project master-baton registry row.
3. Patch for current truth without widening authority:
   - record the proof as completed evidence;
   - replace stale “next proof/smoke” wording with the new smallest safe next action;
   - preserve gate status if it did not move;
   - repeat no-go boundaries near the updated claim.
4. Prefer “current-floor confirmation,” “metadata-only receipt,” “ratchet-not-promotion,” and “do not run another slice by inertia” when the proof was narrow.
5. Verify both absence and presence:
   - stale phrases removed;
   - new proof/SHA/current-safe-next language present;
   - `git diff --check` passes;
   - added lines do not import raw platform IDs, token-shaped strings, credential references, source snippets, or private body text.
6. Write a small reconciliation receipt listing files changed, assertions run, and holds preserved.

## Pitfalls

- Do not treat a PASS proof as a gate promotion unless the evidence explicitly closes the gate.
- Do not leave old “next smoke” wording after the smoke has passed; future agents will replay it by inertia.
- Do not scan for broad words like “credential” as a hygiene failure in no-go docs; scan for credential values, token prefixes, bot-token env names, or imported raw IDs instead.
- Do not mutate historical docs broadly just to remove old language. Patch the active anchors and add/index status where needed.
