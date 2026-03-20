# Workflows

Composable, reusable workflows that can be run independently or composed into orchestrators.

Each workflow focuses on a specific aspect of software development and can be:
- **Run standalone** - Invoke directly when you need to do this specific thing
- **Composed** - Orchestrators combine multiple workflows into end-to-end flows

## Workflow Categories

### Setup
- `capture-preferences` - Capture your interaction preferences
- `bootstrap-project` - Set up a new project structure

### Planning
- `describe-problem` - Describe the problem you're solving
- `define-requirements` - Define product requirements
- `design-ux` - Establish UX design guide
- `design-technical` - Design system architecture
- `review-risks` - Review risks and assumptions
- `create-backlog` - Create product backlog

### Implementation
- `pick-feature` - Select the next feature to implement
- `elicit-requirements` - Elicit functional requirements
- `write-gherkin` - Write Gherkin specification
- `assess-quality-gates` - Assess quality gates (observability, testing, data, rollout)
- `plan-chunks` - Plan implementation chunks and tasks
- `execute-plan` - Execute the implementation plan
- `review-plan` - Review a plan for quality
- `validate-feature` - Validate a completed feature
- `triage-backlog` - Triage and reprioritize backlog

### Operations
- `triage-logs` - Triage production logs and alerts
- `review-incident` - Conduct incident review
- `analyze-root-cause` - Perform root cause analysis
- `assess-risk` - Assess deployment risks

### Git & Release
- `merge-to-trunk` - Merge feature branch to trunk
- `prepare-release` - Prepare a release

## When to Use Workflows

**Use a workflow directly** when:
- You need fine-grained control over a specific step
- You want to skip orchestration and do one thing
- You're debugging or understanding a specific part

**Use an orchestrator** (in `../orchestrators/`) when:
- You want a complete end-to-end workflow
- You want the system to handle everything automatically

## Architecture

```
Orchestrators (Layer 1)
    ↓ compose
Workflows (Layer 2) ← You are here
    ↓ compose
Primitives (Layer 3)
```

See `../orchestrators/README.md` for high-level orchestrators.
See `../primitives/README.md` for internal primitives (do not invoke directly).
