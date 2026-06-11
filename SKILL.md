---
name: update-docs
description: |
  Use when: (1) user says "update docs", "docs are stale", or "/update-docs",
  (2) user asks to document a codebase, create a README, or generate a codebase map,
  (3) user wants CLAUDE.md, AGENTS.md, or agent-focused documentation,
  (4) user says "map this codebase" or "cartographer",
  (5) user is onboarding to a new project and needs architecture overview,
  (6) after significant code changes that may have made existing docs outdated.
  Works with any language or project type.
license: MIT
metadata:
  author: jeremyknows
  version: "1.0.0"
  openclaw:
    requires:
      bins: ["python3"]
---

# Update Docs

## Step 1: Determine Mode

First distinguish broad documentation generation from a bounded stale-doc patch.

### Bounded patch mode

Use **Bounded patch** when the user names exact docs/anchors/rows to update, asks for stale language to be corrected, or authorizes a narrow documentation-only follow-up from a prior gap review. In this mode:

1. Do **not** run a whole-codebase scanner or spawn broad documentation subagents.
2. Read the named docs and the source evidence artifacts/reports that justify the wording.
3. Patch only the stale rows/sections and nearby metadata needed by the change.
4. Preserve authority boundaries explicitly: name what evidence proves, what remains held, and whether any gate/status remains open.
5. Verify old stale phrases/rows are absent, new rows/sections are present, referenced artifact paths exist, `git diff --check` passes, and added lines introduce no raw IDs, secret-looking values, or positive overclaims.
6. **For docs-only anchor hygiene PRs, keep the review surface clean and proof-shaped.** When the task is to ratchet continuity anchors after merged evidence convergence, start from the exact requested `origin/main` source truth in a clean worktree/branch, patch only the named current anchors, and keep wording scanner-stable: prefer “ratchet-not-promotion,” “accepted floor,” and “held surfaces remain held” over phrases that look like service/runtime/Gate promotion. If the handoff gives an exact allowed-file list, treat optional sibling files from earlier artifacts as out of scope unless the current source explicitly re-allows them. Run both added-line hygiene scans and full-doc classification scans; report the distinction clearly because historical anchors may intentionally contain old raw IDs, private paths, or no-go wording. If authorized to open a PR, make it docs-only, include the verifier output in the PR body, verify the PR body back from GitHub (status string, merge floor SHA, residual holds), verify GitHub state/mergeability/head/files, and treat “no checks reported” as its own state rather than a CI pass. Do not merge unless separately authorized.
7. **Patch conservative HOLD state when it is the current truth.** If fresh evidence is partial (for example a relay/report PASS exists but a required durable artifact is missing), do not leave anchors frozen at the previous milestone just because the gate did not advance. Patch the active master baton/scorecard/artifact map/operator-facing anchor to record the held evidence class, the exact missing artifact/path, and the smallest safe next action, while explicitly preserving no-service/no-runtime/no-write/no-Gate authority. HOLD state is valid documentation; it must not be phrased as closure.
7. When patching machine-readable topology/docs for a future visual artifact, distinguish **count/check metadata** from **rendered topology nodes**. Do not add placeholder nodes/edges solely to make a drift count pass if the renderer has visual/layout constraints or the audit only proves a configured surface count. Prefer updating the explicit drift expectation/comment and adding narrative caveats/source-status rows; then run the existing render/check command and treat layout failures as evidence that the topology expansion is not minimal.
7. For patch-only or proof-only PASS evidence where the campaign/gate remains held, use `references/patch-only-proof-anchor-sync.md`: classify the bounded proof, name residual holds, patch every active anchor that would mislead a cold session (master baton/current-state doc, artifact map, gate scorecard/blockers, operator-facing twin anchor, and narrow fixture/checklist plan when applicable), replace stale next-step wording, then run stale-phrase scans and artifact/receipt checks before reporting.
8. For a post-proof “super pass” over anchor docs / twin anchors / master baton / readiness docs, use `references/current-floor-anchor-reconcile.md`: verify the exact current floor, patch active anchors from “next smoke/proof” to “completed current-floor evidence + smallest safe next action,” preserve gate holds, update cross-project baton registries when they would mislead cold sessions, and verify both stale-phrase removal and hygiene of newly added proof metadata. Do not stop at the initially named anchor list: run a whole-active-doc stale-floor scan for old source SHAs, stale “current floor” headers, and superseded next-step phrases across adjacent control-plane/checklist/readiness docs before committing. A clean initial anchor diff can still leave a stale header in a sibling doc that cold sessions trust.
9. Leave changes uncommitted unless the user explicitly asks to commit.
9. For documentation directories that contain multiple historical generations, prefer a directory-level legend/index over deleting or rewriting old docs. Add a concise `README.md`/`_index.md` with: current truth snapshot, label key (`current`, `partly stale`, `superseded`, `reference`), per-file status table, and source-check rule. Then patch only the highest-risk old docs with top notes or narrow status splits. Preserve historical artifacts as labeled context; do not make old docs look current by selectively editing every stale sentence.
10. When a long issue-railed campaign reaches a reconciliation/no-successor stopping point and the user asks how far along the roadmap is or whether the what/why/how docs are accurate, switch from rail execution to **current-state synthesis**. Use `references/current-state-roadmap-synthesis.md`: verify live source/issue/PR/cron state, audit tactical docs vs top-level roadmap gaps, write one operator-readable synthesis doc, link it from the docs index/roadmap, and clearly separate control-plane progress from remaining product/fleet-usefulness holds.

This mode is common for gate scorecards, artifact maps, master batons, historical architecture folders, long issue-railed roadmap campaigns, and other Atlas continuity anchors after a no-live evidence/gap review.

### Full / Update / Map-only modes

Check the project root and `docs/` directory for existing documentation:

- Look for `docs/CODEBASE_MAP.md` — if it exists, read its YAML frontmatter for `last_mapped` timestamp
- Look for `CLAUDE.md` and `README.md` at the project root

Based on findings, select a mode:

| Condition | Mode |
|-----------|------|
| User named exact docs/rows/anchors for a narrow stale-doc correction | **Bounded patch** — patch only that scope |
| No `docs/CODEBASE_MAP.md` exists | **Full** — generate all docs from scratch |
| `CODEBASE_MAP.md` exists with `last_mapped` | **Update** — scope changes since last map |
| User specifically asked for "codebase map" only | **Map-only** — generate only `docs/CODEBASE_MAP.md` |

Default to **Full** only when the user asks for broad docs and scope is ambiguous. Default to **Bounded patch** when exact target files/rows are provided.

## Step 2: Scan the Codebase

Run the bundled scanner to get file tree with token counts:

```bash
uv run {skill_dir}/scripts/scan-codebase.py . --format json
```

Where `{skill_dir}` is the directory containing this SKILL.md (resolve from the skill's own file path).

If `uv` is not available, fall back to:
```bash
python3 {skill_dir}/scripts/scan-codebase.py . --format json
```

Parse the JSON output to extract:
- `files` — list of `{path, tokens, size_bytes}` for each file
- `directories` — list of directory paths
- `total_tokens` — sum of all file tokens
- `total_files` — count of scanned files
- `skipped` — files that were too large, binary, or unreadable

From the scan results, identify:
- **Project type** (web app, CLI tool, library, monorepo, etc.)
- **Primary language and framework**
- **Module groupings** (group files by top-level directory)

**Update mode only:** Also run:
```bash
git log --oneline --since="{last_mapped}"
```
Use the output to identify which modules/directories have changed. Scope subagent work to changed modules only; preserve existing docs for unchanged modules.

## Step 3: Plan & Spawn Subagents

**Token budget:** 150,000 tokens per Sonnet subagent.

Group files by directory/module. Balance token counts across subagents:
- **Small codebases** (<100k total tokens): 1 subagent
- **Medium codebases** (100k–300k): 2–3 subagents
- **Large codebases** (300k+): 4–6 subagents

**CRITICAL: Opus orchestrates, Sonnet reads.** Do NOT read codebase files directly from this orchestrating context. ALL file reading is delegated to subagents.

**CRITICAL: Spawn ALL subagents in a SINGLE message.** Subagents need read-only file access. In Claude Code, use the Task tool with `subagent_type: "Explore"` and `model: "sonnet"`.

Each subagent prompt must include:

1. **File list** — the specific files assigned to this subagent, with their token counts
2. **Per-file analysis request:**
   - Purpose and responsibility
   - Key exports (functions, classes, constants)
   - Key imports and dependencies
   - Patterns and conventions used
   - Gotchas or non-obvious behavior
3. **Per-module analysis request:**
   - How files within the module connect to each other
   - Entry points and public API
   - Data flow through the module
   - Internal patterns unique to this module
4. **Two summary types:**
   - **Agent-focused:** Dense, scannable — exports, patterns, constraints, file relationships
   - **User-focused:** Narrative — what this module does, why it exists, how it fits in the system
5. **Navigation entries:** "To add/modify/debug X in this module, touch these files"

**Update mode:** Include existing doc sections for assigned modules in the subagent prompt. Ask the subagent to report what has changed vs. what remains accurate.

## Step 4: Synthesize Outputs

After all subagents return, merge their reports into documentation files.

Load `{skill_dir}/references/doc-templates.md` for output format templates and mermaid conventions.

Get the actual timestamp (never estimate):
```bash
date -u +"%Y-%m-%dT%H:%M:%SZ"
```

### Files to Write

**`docs/CODEBASE_MAP.md`** (always)
- YAML frontmatter: `last_mapped`, `total_files`, `total_tokens`
- System overview mermaid diagram (flowchart TB, max 20 nodes)
- Annotated directory tree from scan results
- Module guide with key files tables
- Data flow sequence diagrams for key interactions
- Conventions section (naming, errors, config, testing)
- **Navigation guide** — minimum 4 "To do X: touch these files" entries

**Operator-facing macro frameworks** (for long-lived architecture docs)
- Preserve the user's decision vocabulary and mental models (metaphors, north-star sentences, macro positioning) when they help non-specialist stakeholders give useful feedback.
- Put these in the human-facing vision/spec/README, not only in an internal baton.
- Include a boundary note: metaphors guide decisions but contracts/tests/sources still govern truth.
- When a paired master baton exists, use the baton skill's `references/twin-anchor-runtime-campaign.md` pattern: update the baton for continuity/live state and the human-facing doc for architecture narrative in the same scoped change.

**`CLAUDE.md`** (Full and Update modes)
- Dense agent reference: stack, commands, structure, patterns, constraints, env vars
- 50–200 lines, scannable
- Links to `docs/CODEBASE_MAP.md` for full architecture
- If CLAUDE.md exists: update stale sections, preserve accurate content

**`README.md`** (Full mode, or Update mode if README is stale)
- User-facing narrative with progressive disclosure
- If README exists: respect existing structure, only update stale sections
- Do NOT add sections the user hasn't chosen to include

**`AGENTS.md`** (only for projects with 4+ distinct modules)
- Module index table
- Per-module: public API, internal patterns, gotchas

**Update mode:** Preserve unchanged sections. Merge new subagent analysis into existing docs. Update the `last_mapped` timestamp.

## Supply-chain incident mode

If the user flags an active package-manager or supply-chain attack while documentation work is in progress, keep documentation updates on already-present local tools. Avoid `npm install`, `pnpm install`, `yarn install`, `npm audit fix`, dependency upgrades, or executing newly fetched package code unless the user explicitly scopes and clears that risk. Prefer `python3`, `git diff --check`, static reads, and existing repo scripts that do not fetch dependencies. State this caveat in the report when it affects what verification you did or skipped.

## Step 5: Sanity Check

After writing all files, verify:

1. **File paths** — every path referenced in generated docs points to a file that actually exists in the codebase (use Glob to spot-check)
2. **Mermaid syntax** — no unclosed subgraphs, node IDs are alphanumeric + underscores only, all `end` keywords present
3. For deeper cross-document auditing, suggest: "Run `/multi-document-consistency-audit` for a full consistency check"

## Step 6: Report / Commit Decision

Summarize what was done:

- List each file: created, updated, or unchanged
- Note any issues found during sanity check
- State the git status and whether changes were committed or left for review

Default: leave changes uncommitted for user review. Exception: if the user explicitly asked to persist/capture durable docs, or the workspace convention favors committing coherent verified documentation, commit the scoped docs change after verification and report the commit. Do not commit if scope is mixed, review is pending, or generated docs are broad enough that human review is likely required first.

## Rules

1. **Opus orchestrates, Sonnet reads.** Never read codebase files directly from this context. Always delegate to subagents. Even for small projects.
2. **Respect existing docs.** Update stale sections; don't replace accurate content or restructure what works.
3. **Commit policy is scoped, not absolute.** Default to leaving generated docs uncommitted for review. If the user explicitly asks to persist/capture durable docs, or the repo convention supports committing coherent verified doc changes, commit only the scoped documentation after sanity checks and report the commit. Never commit broad/mixed generated docs just to make the tree clean.
4. **Navigation guides are mandatory.** Every CODEBASE_MAP.md must include "To do X: touch these files" entries. This is the most actionable documentation for agents.

## Changelog

- **2026-05-11 — Operator-facing macro frameworks:** Added guidance for long-lived architecture docs to preserve user decision vocabulary/metaphors in human-facing specs, and to use the baton Twin-Anchor pattern when paired continuity docs exist.
