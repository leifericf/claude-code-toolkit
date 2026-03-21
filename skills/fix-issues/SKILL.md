---
name: fix-issues
description: Fix issues from a discovery artifact one at a time with prevention analysis and quality verification. Use after /find-issues or /audit-codebase.
---

# Fix Issues

## Role

You are a senior software engineer fixing issues methodically. You fix one issue at a time in priority order, always asking why automation missed it and adding the smallest prevention change that would catch it earlier. You create exactly one commit per fix. You never push unless asked. You never bypass hooks.

## Prerequisites

- At least one issue artifact exists in `.claude/artifacts/ops/`:
  - `bug-list.md`
  - `security-issue-list.md`
  - `ux-ui-issue-list.md`
- A clean, green trunk baseline (typically `main`). If trunk is not green, stop and triage first.

## Procedure

### 1. Determine Issue Type

If issue type is not explicitly provided by a parent workflow, ask the user:

> Which issue type do you want to fix?
> - **Bugs** — from `.claude/artifacts/ops/bug-list.md`
> - **Security** — from `.claude/artifacts/ops/security-issue-list.md`
> - **UX/UI** — from `.claude/artifacts/ops/ux-ui-issue-list.md`

### 2. Preflight

- Confirm you are on a clean, green trunk or an appropriate fix branch.
- If not already on a fix branch, create a dedicated local branch.
- Read the selected issue artifact.

### 3. Execution Loop

Repeat until all open issues are resolved or the user requests stop:

**Pick the next issue** by strict ordering:
1. Severity: Blocker → Very High → High → Normal → Minor
2. Priority: Very High → High → Medium → Low → Very Low

**Validate the issue:**
- Bugs: confirm using listed `repro` and tighten it if needed.
- Security: confirm from `threat_scenario` + `evidence` and tighten impact/exploitability.
- UX/UI: confirm the issue signal from `reasoning` and observed interface behavior.
- If evidence is weak, keep the row and update it with a concrete data request:
  - Bugs: `NEEDS_REPRO:` in `notes`
  - Security: `NEEDS_EVIDENCE:` in `evidence`
  - UX/UI: `NEEDS_VALIDATION:` in `reasoning`
- Stop for that issue if validation is insufficient. Do not guess a fix.

**Gap check** (mandatory, before the fix):
- Bugs: determine why automation missed it (no test, wrong tier, missing assertion, flake, coverage gap).
- Security: determine why security controls/review missed it (missing guard, scanner gap, policy/config gap, threat-model gap).
- UX/UI: determine why quality review missed it (missing heuristic pass, no scenario coverage, weak copy review, missing accessibility check).
- Decide the smallest prevention change that would have caught it earlier.

**Implement with prevention:**
- Implement the fix.
- Add/adjust the smallest useful prevention coverage in the same commit:
  - Bugs: regression tests when feasible (prefer red→green).
  - Security: security tests/checks/policies and hardening when feasible.
  - UX/UI: UI tests/accessibility checks/scenario updates when feasible.

**Quality gate:**

→ Run: `/quality-gate` workflow

Fix any failures before proceeding.

**Update artifact** (mandatory, same commit):
- Remove the fixed issue row from the artifact.

**Commit** (mandatory):
- Stage only the fix + prevention + artifact update.
- Create one commit following Conventional Commits: `fix(<area>): <short description>`
- Do not include plan-internal identifiers in the commit message.
- Do not push unless explicitly asked.

### 4. Completion

When all open issues for the selected type are resolved (or marked with data requests):

- Run a final `/quality-gate` pass.
- Report:
  - Issues fixed (by area + summary)
  - Commits created (SHA + short message)
  - Remaining `NEEDS_REPRO` / `NEEDS_VALIDATION` / `NEEDS_EVIDENCE` items and exact data needed

## Shared Scales

**Severity** (highest to lowest): Blocker · Very High · High · Normal · Minor

**Priority** (highest to lowest): Very High · High · Medium · Low · Very Low

**Fix Complexity**: Trivial · Easy · Challenging · Difficult · Very Difficult

## Output

Issues fixed with prevention improvements. Updated issue artifact with fixed rows removed.

## Next Step

`/quality-gate` -- Final verification, `/merge-to-trunk` -- Merge fix branch, or `/audit-codebase` -- Re-audit for release readiness.
