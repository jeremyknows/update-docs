# Current-state roadmap synthesis pattern

Use when a long issue-railed campaign reaches a logical stopping point and the user asks how far along the roadmap is, whether docs are accurate, or how to define the path to fleet-wide/full implementation.

## Trigger signs

- A rail/reconciliation doc says no successor issue, no cron mutation, or retry/attempt consumed.
- Tactical docs are current but scattered across many issue packets.
- The user asks for the "what / why / how", comprehensive current state, or path to full implementation.
- A cron/autopilot loop just closed a lane and further automation would be churn without a higher-level decision.

## Pattern

1. Verify live state before writing synthesis:
   - current UTC timestamp;
   - repo branch, `HEAD`, `origin/main`, and worktree status;
   - relevant GitHub issues/PRs all closed/merged;
   - active writer/poke/watch crons are paused or intentionally still active;
   - latest tactical reconciliation packet and index entries are present.
2. Audit documentation shape:
   - tactical rail docs: are they current and indexed?
   - top-level README/ROADMAP: are they accurate but too coarse?
   - private/provenance batons: are they valuable but too long/historical for operator planning?
   - identify whether the gap is factual staleness or missing synthesis.
3. Write one operator-readable synthesis doc with:
   - verified source floor and rail closure facts;
   - executive summary;
   - what the system is and is not;
   - why it exists / risks it controls;
   - how it works today;
   - completed evidence and still-held surfaces;
   - documentation accuracy audit;
   - roadmap progress table;
   - logical stopping-point rationale;
   - phased path to full/fleet implementation;
   - immediate human decisions and non-goals.
4. Link the synthesis from the docs index and roadmap so a cold session finds it before spawning new work.
5. Verify:
   - `git diff --check`;
   - required-string checks for source floor, closed issues/PRs, held surfaces, and cron state;
   - existing project hygiene scans if available;
   - focused tests for the latest rail when applicable;
   - compile/static checks if the repo convention uses them.
6. Leave synthesis docs uncommitted unless the user asks to commit or the repo convention explicitly favors committing verified docs.

## Pitfalls

- Do not turn a zero-item/degraded or HOLD receipt into a success claim. State the useful control-plane progress and the remaining product/usefulness gap separately.
- Do not spawn another successor rail just because the previous rail closed. If the closure packet says no successor/no cron mutation, switch to synthesis and human decision framing.
- Do not bury the answer in cron mechanics. Foreground artifact progress, current source truth, and next implementation choices.
- Treat private batons/provenance docs as source context, not as public/operator-readable roadmap replacements.
