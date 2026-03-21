---
name: remediate-issues
description: Fix all discovered issues across security, bugs, and UX/UI tracks in priority order with parallel execution for independent code areas. Use after /audit-codebase to systematically address findings.
disable-model-invocation: true
---

# Remediate Issues (Orchestrator)

You guide the user through fixing all discovered issues across all issue tracks, using parallel subagents for independent code areas when possible.

## Role

You are a senior software engineer coordinating a structured remediation pass. You analyze issues for parallelization opportunities, fix security issues first (compliance/safety implications), then bugs, then UX/UI issues. You provide checkpoints between tracks and ensure quality gates pass throughout.

## Prerequisites

- At least one issue artifact exists in `.claude/artifacts/ops/`:
  - `bug-list.md`
  - `security-issue-list.md`
  - `ux-ui-issue-list.md`
- A clean, green trunk baseline

## Procedure

### 1. Confirm Remediation Scope

- Read all three issue artifacts to understand the full picture.
- Confirm with the user: all open issues vs selected tracks/areas.
- Note any release target or timeline constraints.

### 2. Analyze and Group Issues

For each issue across all three artifacts:
- Note the `area` column (code module/directory)
- Estimate which files would likely be touched

Group issues into area-based batches:
- Issues in the same area → same batch (shared file context = fewer tokens, faster fixes)
- Issues in adjacent areas with likely shared files → same batch (avoid conflicts)

Classify batches:
- **Independent** (no file overlap between batches) → can run as parallel subagents
- **Overlapping** (shared files between batches) → merge into one batch, run sequentially

Present the grouping to the user:
- Number of issues across number of independent code areas
- Batch breakdown: [area: N issues] per batch
- Which batches can run in parallel vs must be sequential
- User confirms or overrides to force sequential mode

### 3. Fix Security Issues

**If independent batches exist within the security track:**

Launch one `issue-fixer` subagent per independent batch using the Agent tool:
- `subagent_type: issue-fixer`
- `isolation: worktree` (each gets its own isolated git worktree)
- Task prompt contains ONLY: issue type (security), area scope, filtered issue list

Each subagent runs concurrently with a lean, focused context — only its assigned issues and area files.

**If all security issues overlap or user chose sequential:**

→ Run: `/fix-issues` inline

Provide: type = security

### 4. Integrate Security Fixes (if parallel)

If parallel subagents were used:
- Rebase each worktree branch onto the fix branch (`git rebase`)
- Fast-forward merge (`git merge --ff-only`) — never use `--no-ff`
- Result: linear commit history, no merge commits
- If rebase conflicts: stop and report (should be rare given area independence)
- Consolidate issue artifacts: read each worktree's updated artifact, merge all fixed-row removals into the canonical artifact
- Worktrees are cleaned up automatically by Claude Code

### 5. Security Checkpoint

Report:
- Security issues fixed (count)
- Remaining security issues by severity
- Any blockers or missing data

### 6. Fix Bugs

Same parallel/sequential decision as step 3, applied to the bugs track.

**Parallel**: Launch `issue-fixer` subagents per independent batch with `isolation: worktree`.
**Sequential**: → Run: `/fix-issues` inline with type = bugs.

### 7. Integrate Bug Fixes (if parallel)

Same integration procedure as step 4.

### 8. Bug Checkpoint

Report:
- Bugs fixed (count)
- Remaining bugs by severity
- Any blockers or missing data

### 9. Fix UX/UI Issues

Same parallel/sequential decision as step 3, applied to the UX/UI track.

**Parallel**: Launch `issue-fixer` subagents per independent batch with `isolation: worktree`.
**Sequential**: → Run: `/fix-issues` inline with type = ux-ui.

### 10. Integrate UX/UI Fixes (if parallel)

Same integration procedure as step 4.

### 11. Final Quality Gate

Run the full quality check suite directly (format, lint, test, build) and fix any failures.

### 12. Present Summary

Report:
- What was fixed by track (security / bugs / UX/UI)
- Commits created (SHA + short message)
- Remaining issues by track + severity
- Explicit unresolved data requests (`NEEDS_REPRO` / `NEEDS_VALIDATION` / `NEEDS_EVIDENCE`)
- Recommended release status: `GO` | `GO WITH RISKS` | `NO-GO`

## Output

- Issues fixed across all tracks with prevention improvements
- Updated issue artifacts with fixed rows removed
- Final quality gate status

## Next Step

`/audit-codebase` -- Re-audit for verification, or `/merge-to-trunk` -- Merge fix branch to trunk.
