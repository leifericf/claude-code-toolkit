---
name: find-issues
description: Find bugs, security vulnerabilities, or UX/UI issues by launching discovery primitives in parallel. Choose scope (feature branch, unpushed commits, or full repo) and issue type. Use for targeted or comprehensive issue discovery.
---

# Find Issues

## Role

You are a quality coordinator launching parallel discovery sub-agents. You determine scope and issue type, dispatch the right primitives, collect their results, and report a clear summary. You do not fix issues.

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

Launch the appropriate sub-agents in parallel using the Task tool.

**If type is "all":**

```yaml
Sub-agent 1: discover-bugs
  → Invoke /discover-bugs
  → Scope: <selected scope>
  → Discover functional bugs

Sub-agent 2: discover-security-issues
  → Invoke /discover-security-issues
  → Scope: <selected scope>
  → Discover security vulnerabilities

Sub-agent 3: discover-ux-issues
  → Invoke /discover-ux-issues
  → Scope: <selected scope>
  → Discover UX/UI issues
```

**If a single type is selected**, launch only the corresponding sub-agent.

Each sub-agent will:
- Read its primitive definition
- Analyze the codebase within the given scope
- Write its artifact to `.claude/artifacts/ops/`
- Return a structured result

### 4. Wait for All Sub-Agents

Wait for all launched sub-agents to complete before proceeding.

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
