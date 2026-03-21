---
name: find-issues
description: Find bugs, security vulnerabilities, or UX/UI issues by launching discovery primitives in parallel. Choose scope (feature branch, unpushed commits, or full repo) and issue type. Use for targeted or comprehensive issue discovery.
---

# Find Issues

## Role

You are a quality coordinator dispatching parallel discovery skills. You determine scope and issue type, invoke the right discovery skills (each forks into its own isolated `issue-discoverer` subagent automatically via `context: fork`), collect their results, and report a clear summary. You do not fix issues.

## Prerequisites

- Project root directory
- Codebase with code to analyze

## Procedure

### 1. Determine Scope

If scope is not explicitly provided by a parent workflow, ask the user:

> What scope should I analyze?
> - **Feature branch** — changes on the current branch vs its base
> - **Unpushed commits** — local commits not yet pushed to the remote
> - **Full repo** — the entire codebase

### 2. Determine Issue Type

If issue type is not explicitly provided by a parent workflow, ask the user:

> What type of issues should I look for?
> - **Bugs** — functional defects
> - **Security** — vulnerabilities and weaknesses
> - **UX/UI** — experience-quality problems
> - **All** — all three types in parallel

### 3. Launch Discovery

**If type is "all"**, invoke all three discovery skills in parallel:

→ Run: `/discover-bugs` — scope: <selected scope>
→ Run: `/discover-security-issues` — scope: <selected scope>
→ Run: `/discover-ux-issues` — scope: <selected scope>

**If a single type is selected**, invoke only the corresponding discovery skill.

Each discovery skill has `context: fork` with `agent: issue-discoverer`, so it automatically runs in an isolated subagent with read-only tools and the Sonnet model. Each discoverer analyzes the codebase within the given scope, writes its artifact to `.claude/artifacts/ops/`, and returns a structured result.

### 4. Wait for All Discovery Skills

Wait for all launched discovery skills to complete before proceeding.

Do not report results until all have returned.

### 5. Report Results

Present a clear summary:

- Scope analyzed (branch/commits/full repo, commit SHA)
- Issue type(s) searched
- Count of issues found per type and severity
- Artifacts updated
- Any `NEEDS_REPRO` / `NEEDS_VALIDATION` / `NEEDS_EVIDENCE` markers requiring follow-up

## Output

Return a structured object:

```yaml
find_issues:
  scope: <feature branch | unpushed commits | full repo>
  types_searched: <list of types>
  results:
    bugs:
      total: <count>
      by_severity: { Blocker: N, Very High: N, High: N, Normal: N, Minor: N }
      artifact: .claude/artifacts/ops/bug-list.md
    security:
      total: <count>
      by_severity: { Blocker: N, Very High: N, High: N, Normal: N, Minor: N }
      artifact: .claude/artifacts/ops/security-issue-list.md
    ux_ui:
      total: <count>
      by_severity: { Blocker: N, Very High: N, High: N, Normal: N, Minor: N }
      artifact: .claude/artifacts/ops/ux-ui-issue-list.md
  unresolved_markers: <count of NEEDS_* items>
```

## Next Step

`/fix-issues` -- Fix discovered issues, or `/audit-codebase` -- Full audit with readiness verdict.
