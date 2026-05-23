# Patch-only proof anchor sync

Use when a bounded implementation/proof lane returns PASS, but the campaign/gate remains held. The docs job is not to celebrate or promote; it is to make the active anchors reflect the new evidence without widening authority.

## Pattern

1. **Classify the proof class first.** Name exactly what passed: patch-only repair, no-service proof, read-only rehearsal, default-off wrapper audit, etc.
2. **Name the proof limits next.** Before editing docs, write the residual holds as a short list. If the proof did not exercise a surface, that surface remains held even if adjacent tests passed.
3. **Patch all active anchors, not just the master baton.** For Runtime/Gate campaigns, check whether the update belongs in:
   - master baton / current-state continuity doc;
   - artifact map / evidence inventory;
   - gate scorecard / blocker table;
   - operator-facing vision/spec/twin anchor;
   - narrow fixture/checklist plan if stale next-step wording remains there.
4. **Use conservative status labels.** Prefer labels like `patch_only_pass / service_runtime_gate_held` over broad “ready” wording.
5. **Update the next safe step.** Replace obsolete next-step language when a formerly-next proof has already passed. Example: after a degraded/latency proof and wrapper repair pass, “produce degraded fixture” is stale; “package/harvest bundle and prepare explicit service-wrapper unhold decision” is current.
6. **Run stale-phrase scans.** Search active anchors for old next-step phrases and over-broad positive claims. Include status-label needles from the pre-merge state (for example `pr_open_*`, “uncommitted bundle,” “package/harvest,” or “before this docs patch”) so artifact-map rows do not keep obsolete package/PR status after merge. Zero-hit scans over a small needle set are useful evidence.
7. **Verify artifact existence and receipt custody.** If the proof came from Dispatch/worker lanes, verify both the report artifact and raw `task_completed` event before citing it in docs.
8. **After a proof PR merges, update evidence-layer wording, not gate posture.** Patch artifact rows from open/local/package status to merged local-proof status, add the merge commit, and replace “package/harvest” with the next decision/action. Preserve holds for adjacent surfaces (service/runtime/credentials/global config/writes/provider/Gate) instead of implying the merge promoted them.

## Pitfalls

- **PASS is not promotion.** A patch-only PASS can reduce uncertainty while still leaving the campaign HOLD.
- **One anchor updated is not enough.** Cold sessions often read the scorecard or artifact map first; if those are stale, the master baton alone will not save them.
- **Do not leave old “next safe step” text alive.** Stale next-step text causes duplicate worker lanes and re-runs of already-passed evidence.
- **Avoid broad hold collapse.** Separate “commit/PR/package promotion” from “service/listener exposure” from “Gate closure”; each has a different unhold path.

## Receipt wording

Good final wording:

> Docs now record `<proof>` as PASS for `<bounded scope>` while preserving `<held surfaces>`. Next safe step is `<specific next action>`, not `<tempting broader action>`.

Bad final wording:

> Gate is basically ready now.
