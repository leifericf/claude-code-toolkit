# Claude Code Agentic Toolkit

A set of composable skills for software development, organized into three layers for maximum flexibility and parallel execution.

## Quick Start

### Most Common: Implement a Feature

```bash
/implement-feature
```

One command handles the complete implementation workflow:
1. Pick feature from backlog
2. Create implementation plan
3. Implement with quality gates
4. Validate the feature

### Other High-Level Workflows

```bash
/design-system        # Design a system from problem to backlog
/incident-response     # Handle incidents from triage to follow-up
/setup-project         # Set up a new project
/merge-to-trunk        # Merge a feature branch to trunk
/prepare-release       # Prepare a versioned release
```

### Fine-Grained Control

Need more control? Run individual workflows directly:

```bash
# Planning workflows
/describe-problem
/define-requirements
/design-ux
/design-technical
/create-backlog

# Implementation workflows
/pick-feature
/plan-feature
/execute-plan
/review-plan
/validate-feature
/triage-backlog

# Operations workflows
/triage-logs
/review-incident
/analyze-root-cause
/assess-risk
```

## Installation

```bash
# Clone the toolkit somewhere on your machine:
git clone https://github.com/leifericf/claude-code-toolkit.git ~/claude-code-toolkit

# Symlink into your project (macOS, Linux, WSL):
mkdir -p /path/to/your/project/.claude
ln -s ~/claude-code-toolkit/workflows /path/to/your/project/.claude/workflows
ln -s ~/claude-code-toolkit/orchestrators /path/to/your/project/.claude/orchestrators
ln -s ~/claude-code-toolkit/primitives /path/to/your/project/.claude/primitives

# Or copy if you prefer a standalone snapshot:
cp -r ~/claude-code-toolkit/workflows /path/to/your/project/.claude/workflows
cp -r ~/claude-code-toolkit/orchestrators /path/to/your/project/.claude/orchestrators
cp -r ~/claude-code-toolkit/primitives /path/to/your/project/.claude/primitives
```

**Note:** Symlinks work on macOS, Linux, and WSL. Native Windows does not reliably support symlinks — use WSL or copy instead.

**Note:** Claude Code's sandbox may block symlinks that point outside the project directory. If you encounter sandbox errors, use the copy approach instead.

Then run `/setup-project` in Claude Code to initialize the project's artifact structure.

## Architecture: Three Layers

```
┌─────────────────────────────────────────────────────────────┐
│ LAYER 1: Orchestrators (High-Level Intent)                  │
│ "I want to accomplish X"                                     │
│ Examples: /implement-feature, /design-system                 │
└─────────────────────────────────────────────────────────────┘
                           ↓ compose
┌─────────────────────────────────────────────────────────────┐
│ LAYER 2: Workflows (Declarative Processes)                  │
│ "I need to do this specific thing"                            │
│ Examples: /plan-feature, /execute-plan, /design-technical    │
└─────────────────────────────────────────────────────────────┘
                           ↓ compose
┌─────────────────────────────────────────────────────────────┐
│ LAYER 3: Primitives (Atomic Assessments)                    │
│ Internal units launched by workflows (do not invoke directly)│
│ Examples: assess-observability, check-format                │
└─────────────────────────────────────────────────────────────┘
```

### Layer 1: Orchestrators (`orchestrators/`)

**High-level entry points you invoke directly.** Each orchestrator composes multiple workflows into complete end-to-end flows.

- **Most of the time**: You'll run an orchestrator
- **Benefit**: One command handles everything automatically
- **Count**: 6 orchestrators

### Layer 2: Workflows (`workflows/`)

**Composable workflows you can run standalone or composed into orchestrators.** Each workflow focuses on a specific aspect of software development.

- **Sometimes**: You'll run a workflow directly for fine-grained control
- **Benefit**: Each workflow is independently useful and reusable
- **Count**: 18 workflows

### Layer 3: Primitives (`primitives/`)

**Internal atomic units launched automatically by workflows.** Do not invoke these directly.

- **Never**: You'll invoke primitives directly
- **Benefit**: Enable parallel execution, reusable components
- **Count**: 8 primitives

## Design Principles

1. **Decompose**: Complex workflows → small focused primitives
2. **Compose**: Recombine primitives into higher-level orchestrators
3. **Reuse**: Same primitive in multiple contexts
4. **Parallelize**: Independent primitives run concurrently
5. **Simplicity**: Each component does one thing well
6. **Directness**: Clear entry points; no ambiguity
7. **Agnosticism**: High-level, declarative, works with any tech stack

## Tech Stack Agnostic

This toolkit captures **general values, principles, and concepts** that apply to software development using **any** tech stack.

**What we focus on**:
- What to build (outcomes, requirements)
- Why it matters (problems, user needs)
- How we'll know it works (acceptance criteria, testing)
- What could go wrong (risks, edge cases)

**What we avoid**:
- Package managers (npm vs cargo vs pip)
- Test runners (jest vs pytest vs cargo test)
- Frameworks (React vs Django vs Phoenix)
- Cloud providers (AWS vs GCP vs Azure)

## Parallel Execution

Workflows launch primitives in parallel for faster execution:

**Sequential** (old): 4 assessments take 4 minutes
```
assess-observability (1 min)
  → wait
assess-testing (1 min)
  → wait
assess-data (1 min)
  → wait
assess-rollout (1 min)
Total: 4 minutes
```

**Parallel** (new): 4 assessments take 1 minute
```
assess-observability (1 min)
assess-testing (1 min)
assess-data (1 min)
assess-rollout (1 min)
  → all run concurrently
Total: 1 minute
```

## Directory Structure

```
claude-code-toolkit/
├── orchestrators/          # Layer 1: High-level entry points
│   ├── README.md
│   ├── implement-feature/
│   ├── design-system/
│   ├── incident-response/
│   ├── setup-project/
│   ├── git/
│   │   └── merge-to-trunk/
│   └── release/
│       └── prepare-release/
│
├── workflows/              # Layer 2: Composable workflows
│   ├── README.md
│   ├── setup/
│   ├── planning/
│   ├── implementation/
│   ├── ops/
│   └── README.md
│
└── primitives/             # Layer 3: Internal atomic units
    ├── README.md
    ├── assess-observability.md
    ├── assess-testing.md
    ├── assess-data.md
    ├── assess-rollout.md
    ├── check-format.md
    ├── check-lint.md
    ├── check-tests.md
    └── check-build.md
```

## How to Use

### For Most Users

Run an orchestrator:
```bash
/implement-feature
```

The orchestrator will:
1. Guide you through the complete workflow
2. Invoke the right workflows at the right time
3. Launch primitives in parallel when beneficial
4. Report progress clearly

### For Power Users

Run workflows directly:
```bash
/plan-feature
```

You get fine-grained control over each step.

### For Developers (Composing New Workflows)

Launch primitives via the Task tool:
```yaml
Launch 4 primitives in parallel:
  → Task: Read primitives/assess-observability.md
  → Task: Read primitives/assess-testing.md
  → Task: Read primitives/assess-data.md
  → Task: Read primitives/assess-rollout.md
Wait for all to complete
Merge results
```

## Artifact Structure

Created by `/setup-project` inside your project's `.claude/` directory:

```
.claude/
  workflows/              # The toolkit (copied from this repo)
    planning/              # Backlog, requirements, problem description, designs
      tasks/               # Feature implementation plans
    decisions/             # Decision log, open questions
    project/               # Project meta, release tracking
    ops/                   # Incident reviews, RCAs, risk assessments
```

Commit `workflows` and `artifacts` to your repo so the team shares them. Add a `.claude/.gitignore` to exclude personal files:

```gitignore
# Personal settings (not shared)
settings.local.json

# Temporary plans (conversation-scoped)
plans/

# Claude Code memory (per-user, not project docs)
projects/
```

## Workflows

### Full Planning Cycle
```
/setup-project → /describe-problem → /define-requirements →
/review-risks → /design-ux → /design-technical →
/create-backlog
```

### Implementation Loop
```
/design-system → /implement-feature →
/incident-response → (repeat)
```

### Ship
```
/merge-to-trunk → /prepare-release
```

## Origins

This toolkit is a Claude Code-native port of the [Agentic Software Delivery Toolkit](https://github.com/leifericf/agentic-software-delivery-toolkit), which is tool-agnostic. This edition replaces the generic file-reference chains with Claude Code's native skill system and adds a three-layer architecture for better composition and parallel execution.

## License

MIT
