---
name: assess-quality-gates
description: Assess multiple quality dimensions in parallel (observability, testing, data, rollout).
entry_point: true
---

# Assess Quality Gates

You assess multiple quality dimensions for a feature by launching parallel sub-agents.

## Role

You are a quality coordinator orchestrating parallel assessments across multiple quality dimensions. You launch sub-agents simultaneously, collect their results, and merge them into a coherent quality assessment.

## Prerequisites

- Gherkin specification for the feature
- Feature context and scope
- Technical context (architecture, integrations, data flows)

## Procedure

### 1. Review Input

Read the Gherkin specification and context provided by the parent workflow.

Understand:
- What the feature does
- What systems/components it touches
- What data flows through it
- What the risk level is

### 2. Launch Parallel Assessments

Launch 4 sub-agents in parallel using the Task tool:

```yaml
Sub-agent 1: assess-observability
  → Read primitives/assess-observability.md
  → Assess observability needs (logging, metrics, tracing)

Sub-agent 2: assess-testing
  → Read primitives/assess-testing.md
  → Define testing strategy (Tier 0/1/2)

Sub-agent 3: assess-data
  → Read primitives/assess-data.md
  → Plan data schema changes, migrations, backfills

Sub-agent 4: assess-rollout
  → Read primitives/assess-rollout.md
  → Define deployment strategy and verification
```

Each sub-agent will:
- Read its primitive definition
- Assess the feature for its dimension
- Return a structured object with findings

### 3. Wait for All Sub-Agents

Wait for all 4 sub-agents to complete before proceeding.

Do not merge results until all 4 have returned.

### 4. Merge Results

Combine the structured objects from all 4 sub-agents into a single quality assessment:

```yaml
quality_gates:
  observability: <from assess-observability>
  testing: <from assess-testing>
  data: <from assess-data>
  rollout: <from assess-rollout>
```

### 5. Return to Parent Workflow

Return the merged quality assessment to the parent workflow (e.g., plan-feature).

The parent workflow will:
- Incorporate the assessment into the plan
- Surface any N/A findings with reasons
- Highlight any required gates that need attention

## Parallel Execution Benefits

By running assessments in parallel:
- **Speed**: 4 dimensions assessed concurrently instead of sequentially
- **Consistency**: Each dimension gets focused attention from a specialist
- **Independence**: Each assessment is self-contained and doesn't block others

## Example

**Input**: Gherkin spec for a payment processing feature

**Output**:
```yaml
quality_gates:
  observability:
    applicability: Required
    failure_modes:
      - Payment gateway timeout → Queue payment, retry with backoff
    logs:
      events: [payment_attempt, payment_success, payment_failure]
    signals:
      - name: payment_success_rate
        healthy_threshold: "> 95%"
  testing:
    tier_0:
      required: true
      approach: Unit tests with mocked dependencies
    tier_1:
      required: true
      approach: Integration tests with test database
    tier_2:
      required: false
  data:
    applicability: Required
    migration_type: schema_only
    up_migration:
      description: Add payments table and indexes
  rollout:
    applicability: Required
    strategy: feature_flag
    guardrails:
      - trigger: Checkout error rate spike > 5%
        action: rollback immediately
```

## Note

This workflow orchestrates parallel execution but can also be run standalone.

However, it's most commonly called by other workflows (e.g., plan-feature) as part of a larger process.

## Output

Return a structured object to the parent workflow:

```yaml
quality_gates:
  observability:
    applicability: Required | N/A
    <other fields from assess-observability primitive>
  testing:
    tier_0: {...}
    tier_1: {...}
    tier_2: {...}
    <other fields from assess-testing primitive>
  data:
    applicability: Required | N/A
    <other fields from assess-data primitive>
  rollout:
    applicability: Required | N/A
    <other fields from assess-rollout primitive>
```

## Next Step

Return results to parent workflow (e.g., plan-feature) for incorporation into the plan.
