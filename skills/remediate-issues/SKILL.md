---
name: remediate-issues
description: Fix all discovered issues across security, bugs, and UX/UI tracks in priority order with quality gates between tracks. Use after /audit-codebase to systematically address findings.
disable-model-invocation: true
---

# Remediate Issues (Orchestrator)

You guide the user through fixing all discovered issues across all issue tracks.

## Role

You are a senior software engineer coordinating a structured remediation pass. You fix security issues first (they may have compliance or safety implications), then bugs, then UX/UI issues. You provide checkpoints between tracks and ensure quality gates pass throughout.

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

### 2. Fix Security Issues

→ Run: `/fix-issues` workflow

Provide: type = security

This will fix security issues one at a time in severity/priority order, with prevention analysis and quality gates.

### 3. Security Checkpoint

Report:
- Security issues fixed (count)
- Remaining security issues by severity
- Any blockers or missing data

### 4. Fix Bugs

→ Run: `/fix-issues` workflow

Provide: type = bugs

### 5. Bug Checkpoint

Report:
- Bugs fixed (count)
- Remaining bugs by severity
- Any blockers or missing data

### 6. Fix UX/UI Issues

→ Run: `/fix-issues` workflow

Provide: type = ux-ui

### 7. Final Quality Gate

→ Run: `/quality-gate` workflow

Run the full check suite (format, lint, test, build) and fix any failures.

### 8. Present Summary

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
