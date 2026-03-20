---
name: plan-feature
description: Turn a selected backlog item into slices, tasks, and a concrete implementation plan with Gherkin specification. Create a detailed plan with observability, testing strategy, and task breakdown. Use after picking a feature.
---

# Plan Feature

## Role

You are a senior software engineer delivering incrementally. You turn a selected backlog item into slices, tasks, and a concrete plan. You prefer user-facing language in questions and avoid exposing technical decisions unless unavoidable. If you must ask about a technical choice, translate it into a user-visible tradeoff.

You follow trunk-based development with Conventional Commits. You use safe, reversible Git operations and never push without being asked. You never bypass Git hooks.

## Prerequisites

- A feature has been selected (either via `/pick-feature` or stated by the user)
- `.claude/artifacts/planning/product-backlog.md` exists
- `.claude/artifacts/planning/product-requirements.md` exists (if available)
- `.claude/artifacts/decisions/decision-log.md` exists (if available)
- `.claude/artifacts/decisions/open-questions.md` exists (if available)

## Procedure

### 1. Check for Blockers

Read `.claude/artifacts/decisions/open-questions.md`. If any unchecked `[Blocking]` item affects this feature, stop and ask the user for an answer before proceeding.

### 2. Functional Elicitation Gate

Before drafting chunks or tasks, capture a crisp functional snapshot of the story. Read the backlog item being implemented and scan the requirements for relevant context. Incorporate any resolved answers from open questions.

If functional requirements are missing or ambiguous, ask questions in this order (cap at 1-2 batches of max 3 questions each):

1. **Problem and intent** -- what problem are we solving and why
2. **Success criteria** -- observable outcomes that mean "done"
3. **User outcomes** -- what the user must be able to do; what must never happen; most important edge cases
4. **User flow** -- primary path, alternate paths, current friction points
5. **Business and domain rules** -- rules that govern behavior
6. **Integrations** -- external systems, data flow, dependency constraints, failure expectations
7. **Prioritization** -- minimal viable increment (MVI), what must be correct first, what can be deferred
8. **Constraints** -- privacy/security, regulatory/compliance, operational limitations (not timelines)
9. **Delight and quality bar** -- what would exceed expectations; what would feel broken

**"Good enough to start" checklist** -- do not proceed until you can answer in plain language:
- Who is the primary user and what are they trying to accomplish?
- What does success look like (observable outcomes)?
- What is the primary user flow?
- What must never happen?
- What are the key edge cases and expected behavior?
- What business rules apply?
- What integrations exist and what happens when they fail (if applicable)?
- What is the MVI and what is explicitly deferred?

If any item is unknown and blocks correct delivery, add it to `.claude/artifacts/decisions/open-questions.md` as `[Blocking]` and stop.

### 3. Write the Gherkin Specification

Write a Gherkin specification for the selected story. Rules:

- Treat Gherkin as the source of truth for feature-level acceptance.
- Use business language and user-observable outcomes. Avoid UI mechanics unless no better abstraction exists.
- Target 3-7 scenarios for the MVI.
- Make scenarios deterministic (avoid time-dependent or non-deterministic data unless that is the subject under test; prefer concrete example values).
- Include at least:
  - One happy-path outcome
  - One "must never happen" scenario (error, access control, invariant, data loss, etc.)
  - One key edge-case behavior
  - One integration/dependency failure or degraded behavior (when applicable)

Structure:
- Use `Feature:` with a short narrative.
- Use `Background:` only for truly shared preconditions.
- Use `Rule:` to group business rules when helpful.
- Use `Scenario Outline:` + `Examples:` for input permutations.
- Each scenario describes one behavior. Given = preconditions; When = action; Then = observable result.
- Assert externally visible state (API response, persisted state, user-facing text) over internal details.

### 4. Assess Quality Gates

Run `/assess-quality-gates` to evaluate observability, testing, data, and rollout concerns in parallel. Review the returned assessment and incorporate the results into the plan artifact (section by section).

### 5. Plan Chunks and Tasks

Decompose into 2-6 independently testable chunks. Each chunk should deliver user value, be testable, and reduce risk. Plan the next chunk in full detail; keep later chunks lighter.

Write tasks so execution can be "commit-per-task": each leaf checkbox should be completable in a single commit.

**Backlog hygiene during planning**: if unrelated "later" ideas come up, capture them under `Inbox (untriaged)` in `.claude/artifacts/planning/product-backlog.md`. Do not expand the current feature's scope unless the user explicitly changes the definition of done.

**Release tasks** (if the user requests a release during planning):
1. Ask whether this is a release candidate (for validation) or a proper release (for production). Block until answered.
2. Review changes since the last release tag, suggest `major`, `minor`, or `patch` with reasoning. The user decides. Block until answered.
3. Add appropriate release tasks (bump version, update release notes, create tag).

### 6. Write the Plan Artifact

Write: `.claude/artifacts/planning/tasks/plan-<feature_slug>.md`

Use this structure (adjust as needed):

```md
# Plan: <Feature Name>

## Metadata
- Date: YYYY-MM-DD
- Backlog item: <epic/story name>
- Feature slug: <feature_slug>

## Context
- Intended outcome: <short>

## Functional Snapshot
- Problem: <1-2 sentences>
- Target user: <who + context>
- Success criteria (observable):
  - <outcome>
- Primary user flow:
  1. <step>
  2. <step>
- Alternate flows (optional):
  - <alt flow>
- Must never happen:
  - <unacceptable outcome>
- Key edge cases:
  - <edge case> -> <expected behavior>
- Business rules:
  - <rule>
- Integrations (if any):
  - <system> - <what data moves / what the user expects>
  - Failure behavior: <what happens when the dependency is down>
- Non-functional requirements:
  - Privacy/Security: <expectation>
  - Performance: <expectation>
  - Reliability: <expectation>
- Minimal viable increment (MVI): <smallest valuable version>
- Deferred:
  - <explicitly not in this feature>

## Executable Specification (Gherkin)

Feature: <short capability name>
  <1-2 sentence narrative>

  Scenario: <happy path>
    Given <precondition>
    When <action>
    Then <observable outcome>

  Scenario: <must never happen>
    Given <precondition>
    When <action>
    Then <unacceptable outcome is prevented>

  Scenario: <key edge case>
    Given <precondition>
    When <action>
    Then <expected behavior>

## Baseline Gate
- Start from a clean, green trunk. If not green, stop and fix first.
- Sync latest trunk before branching.
- Local feature branches for development.

## Architecture Fit
- Touch points: <key components/boundaries affected>
- Compatibility notes: <what must remain backward-compatible>

## Observability (Minimum Viable)
- Applicability: Required | N/A (Reason: <one line>)
- Failure modes:
  - <failure mode> -> <expected degraded behavior>
- Logs (structured): <events + key fields; no sensitive data>
- Signals/metrics: <1-3 key signals>

## Testing Strategy (Tier 0/1/2)
- Tier 0 (required): <unit/contract tests>
- Tier 1 (if applicable): <deterministic integration tests>
- Tier 2 (if applicable): <sandbox/third-party validation>

## Data and Migrations
- Applicability: N/A | Schema only | Backfill | Expand/contract
- Up migration: <approach>
- Down migration: <yes/no + why>
- Backfill plan: <if any>
- Rollback considerations: <what happens on revert>

## Rollout and Verify
- Applicability: Required | N/A (Reason: <one line>)
- Strategy: Feature flag | Canary | Staged | All-at-once
- Verify (smoke path): <2-6 steps>
- Signals to watch: <1-3>

## Cleanup Before Merge
- Remove: <temporary flags/debug logs/spike code/transitional scaffolding>
- If anything temporary remains: <decision log row + follow-up backlog item>
- Squash intermediate commits into logical commits
- Ensure all commits follow Conventional Commits
- Rebase onto trunk and merge with fast-forward only

## Definition of Done
- Gherkin specification is complete and current in the plan artifact
- Tier 0 green; Tier 1 green when applicable
- Observability requirements implemented (or explicitly N/A)
- Cleanup gate satisfied
- Backlog updated (shipped item moved to "In product (shipped)")

## Chunks
- <chunk name>
  - User value: <short>
  - Scope: <short>
  - Ship criteria: <short, testable>
  - Rollout notes: <flags/migrations/backfill or none>

## Relevant Files (Expected)
- `<path>` - <why>

## Notes
- Include tests where appropriate.
- Include any migrations, backfills, or feature flags explicitly.
- Prefer small, reviewable commits that map to chunks.
- Each chunk should deliver user value, be testable, and reduce risk.

## Assumptions
- <assumption made to proceed>

## Validation Script (Draft)
1. <step>
2. <step>

## Tasks
- [ ] T-001 Create and checkout a local branch (e.g. `feature/<feature_slug>`)

- [ ] Implement: <chunk name>
  - [ ] T-010 <leaf task completable in one commit>
  - [ ] T-011 <leaf task completable in one commit>
  - [ ] T-012 Tests: <what to add>

- [ ] Quality gate
  - [ ] T-900 Run formatters
  - [ ] T-901 Run linters
  - [ ] T-902 Run tests

- [ ] Merge to trunk
  - [ ] T-950 Squash intermediate commits into logical commits
  - [ ] T-951 Ensure all commits follow Conventional Commits
  - [ ] T-952 Rebase onto trunk and merge (fast-forward only)

## Open Questions
- <any non-blocking questions or none>
```

## Output

`.claude/artifacts/planning/tasks/plan-<feature_slug>.md`

## Next Step

`/review-plan` (optional) or `/execute-plan` -- Review the plan for quality or start implementation.
