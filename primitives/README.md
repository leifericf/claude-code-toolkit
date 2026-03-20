# Primitives

Internal implementation details launched automatically by workflows.

**Do not invoke these directly.** These primitives are designed to be called by workflows via the Task tool.

## Purpose

Primitives enable:
- **Parallel execution** - Multiple primitives run simultaneously
- **Direct object passing** - No intermediate artifacts needed
- **Reusable components** - Shared across workflows

## How Primitives Work

Workflows launch primitives to perform specific, focused tasks:

```yaml
Workflow: assess-quality-gates
  → Launch 4 primitives in parallel:
     - assess-observability
     - assess-testing
     - assess-data
     - assess-rollout
  → Wait for all 4 to complete
  → Merge results
```

## Available Primitives

### Quality Assessment Primitives
- `assess-observability` - Determine observability needs
- `assess-testing` - Define testing strategy
- `assess-data` - Plan data and migrations
- `assess-rollout` - Define rollout strategy

### Quality Gate Primitives
- `check-format` - Run code formatters
- `check-lint` - Run linters
- `check-tests` - Run tests
- `check-build` - Run build

## Tech Stack Agnostic

Primitives are **high-level and declarative**. They help you think through universal concerns:

- "What events should we log at boundaries?"
- "What metrics indicate healthy vs. broken?"
- "What failure modes should we anticipate?"

**NOT** tech-stack specific:
- No: "Use OpenTelemetry"
- No: "Log to CloudWatch"
- No: "Install this library"

You choose the implementation. The primitive helps you think through the concern.

## For Users

If you're looking for commands to run, see:
- `../orchestrators/` - High-level workflows you invoke
- `../workflows/` - Mid-level workflows you can run standalone

## For Developers

When adding new primitives:

1. Make them **focused** - Do one thing well
2. Make them **agnostic** - Ask questions, don't prescribe tools
3. Make them **composable** - Can be used in multiple contexts
4. Add `user-invocable: false` to frontmatter
5. Add `allowed-tools: ["Bash"]` if the primitive runs shell commands

## Architecture

```
Orchestrators (Layer 1)
    ↓ compose
Workflows (Layer 2)
    ↓ compose
Primitives (Layer 3) ← You are here
```
