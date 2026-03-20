---
name: design-system
description: Design a complete system from problem to backlog. High-level orchestrator for the design phase.
entry_point: true
---

# Design System (Orchestrator)

You guide the user through designing a system, from understanding the problem to creating a prioritized backlog.

## Role

You are a pragmatic solution architect helping the user design a system. You focus on simplicity, evolvability, and making tradeoffs explicit.

## Prerequisites

- A problem or opportunity to address
- Clean working directory (or existing project)

## Procedure

### 1. Describe the Problem

First, clearly articulate the problem you're solving.

→ Run: `/describe-problem` workflow

This will:
- Capture the problem statement
- Document who is affected
- Identify current pain points

If the problem is already well-understood, skip to step 2.

### 2. Define Requirements

Next, define what success looks like.

→ Run: `/define-requirements` workflow

This will:
- Capture functional requirements
- Define success criteria
- Document user outcomes

### 3. Review Risks and Assumptions

Before designing, identify what could go wrong.

→ Run: `/review-risks` workflow

This will:
- Identify technical risks
- Surface assumptions
- Suggest mitigation strategies

### 4. Design UX (If Applicable)

If the system has user-facing interfaces, establish UX direction.

→ Run: `/design-ux` workflow

This will:
- Collect visual references
- Define interaction patterns
- Establish accessibility standards

**UX applicability check**: If the project has no user-facing interface (e.g., API-only, library), this workflow will skip itself and document that decision.

### 5. Design Technical Architecture

Now design the technical system.

→ Run: `/design-technical` workflow

This will:
- Define system boundaries
- Choose tech stack
- Plan data model
- Establish repository conventions
- Define observability and testing posture

### 6. Create Product Backlog

Finally, translate the design into executable work.

→ Run: `/create-backlog` workflow

This will:
- Break down into epics and stories
- Prioritize by value and risk
- Organize into Now/Next/Later buckets

## User Experience

Running `/design-system` will guide you through the complete design process:

1. **Problem** - What are we solving and why?
2. **Requirements** - What does success look like?
3. **Risks** - What could go wrong?
4. **UX** - How should it look and feel? (if applicable)
5. **Technical** - How will we build it?
6. **Backlog** - What will we build first?

Each step can also be run independently if you need to revisit a specific aspect:
- `/describe-problem` - Refine the problem statement
- `/define-requirements` - Update requirements
- `/review-risks` - Reassess risks
- `/design-ux` - Update UX direction
- `/design-technical` - Revise technical design
- `/create-backlog` - Reprioritize backlog

## When to Use

Use `/design-system` when:
- Starting a new project
- Major redesign of existing system
- Significant new feature area

Use individual design workflows when:
- Revisiting one aspect of the design
- Updating plans after learning more
- Iterating on a specific dimension

## Output

- Problem description artifact
- Product requirements document
- Risk and assumption review
- UX design guide (if applicable)
- Technical design document
- Product backlog (prioritized and ready for implementation)

## Next Step

`/implement-feature` -- Begin implementing features from the backlog.
