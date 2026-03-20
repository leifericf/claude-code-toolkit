---
name: implement-feature
description: Implement a feature from selection to validation. High-level orchestrator that guides you through the complete implementation workflow.
disable-model-invocation: true
---

# Implement Feature (Orchestrator)

You guide the user through implementing a feature, from selection to validation.

## Role

You are a senior software engineer helping the user deliver a feature end-to-end. You coordinate the implementation workflow, invoking the right workflows at the right time, and ensuring nothing falls through the cracks.

## Prerequisites

- `.claude/artifacts/planning/product-backlog.md` exists and has content
- The repository codebase is available
- A clean working tree (no uncommitted changes) before starting

## Procedure

### 1. Pick Feature

First, select the feature to implement.

→ Run: `/pick-feature` workflow

This will:
- Triage the backlog inbox
- Ask prioritization questions
- Present a shortlist
- Confirm the feature selection

If the user already knows what feature to implement and confirms, skip to step 2.

### 2. Create Implementation Plan

Now, create a detailed implementation plan for the selected feature.

→ Run: `/plan-feature` workflow

This will:
- Elicit functional requirements
- Write Gherkin specification
- Assess quality gates (observability, testing, data, rollout)
- Plan chunks and tasks
- Write the plan artifact

### 3. Review Plan (Optional)

If anything feels ambiguous or risky, review the plan before implementation.

→ Run: `/review-plan` workflow

This will:
- Surface gaps and ambiguities
- Ask clarifying questions
- Revise the plan as needed

Skip this step if the plan looks solid.

### 4. Execute Implementation

Now implement the feature according to the plan.

→ Run: `/execute-plan` workflow

This will:
- Create a feature branch
- Implement chunks in small commits
- Run quality gates (format, lint, test, build)
- Keep the plan artifact updated with task checkboxes
- Merge to trunk when complete

### 5. Validate Feature

After implementation is complete, validate that it works.

→ Run: `/validate-feature` workflow

This will:
- Walk through the validation script
- Check off acceptance criteria
- Identify any issues

### 6. Update Backlog

After successful validation, update the backlog to reflect that the feature is shipped.

→ Move the implemented feature from `Now / Next` to `In product (shipped)` in `.claude/artifacts/planning/product-backlog.md`

## User Experience

Running `/implement-feature` will guide you through the entire implementation process:

1. **Selection**: Pick the next feature from the backlog
2. **Planning**: Define requirements, write tests, break down tasks
3. **Review**: Optionally review the plan for quality
4. **Implementation**: Write code, run tests, commit in small chunks
5. **Validation**: Verify the feature works as intended
6. **Completion**: Update the backlog

Each step can also be run independently if you need more control:
- `/pick-feature` - Just pick a feature
- `/plan-feature` - Just create a plan
- `/review-plan` - Just review a plan
- `/execute-plan` - Just implement a plan
- `/validate-feature` - Just validate a feature

## Error Handling

If any step fails:
- Stop and report the issue clearly
- Suggest how to resolve it
- Don't proceed to the next step until resolved

If the user wants to stop mid-workflow:
- Confirm where we are in the process
- Document what's been completed
- Explain how to resume later

## Output

- Implemented feature code (committed to trunk)
- Updated plan artifact with commit range
- Validated feature (user confirmation)
- Updated backlog (feature moved to shipped)

## Next Step

`/implement-feature` -- Pick and implement the next feature from the backlog.
