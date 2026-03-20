# Agentic Software Delivery Toolkit for Claude Code

A portable set of Claude Code skills that provide a complete software delivery workflow — from problem definition through production operations.

Drop the `skills/` directory into any project's `.claude/skills/` and get structured planning, implementation, git, release, and operations workflows.

## Installation

```bash
# Clone the toolkit somewhere on your machine:
git clone https://github.com/leifericf/claude-code-toolkit.git ~/claude-code-toolkit

# Symlink the skills into your project (macOS, Linux, WSL):
mkdir -p /path/to/your/project/.claude
ln -s ~/claude-code-toolkit/skills /path/to/your/project/.claude/skills

# Or copy if you prefer a standalone snapshot:
cp -r ~/claude-code-toolkit/skills /path/to/your/project/.claude/skills
```

**Note:** Symlinks work on macOS, Linux, and WSL. Native Windows does not reliably support symlinks — use WSL or copy instead.

**Note:** Claude Code's sandbox may block symlinks that point outside the project directory. If you encounter sandbox errors, use the copy approach instead.

Then run `/setup/bootstrap` in Claude Code to initialize the project's artifact structure.

## Quick Start

1. **Bootstrap**: `/setup/bootstrap` — creates `.claude/artifacts/` and initial files
2. **Plan**: `/planning/describe-problem` — start the planning workflow
3. **Build**: `/implementation/pick-feature` — start the implementation workflow
4. **Ship**: `/git/merge-to-trunk` — merge to trunk with linear history
5. **Release**: `/release/prepare-release` — create a versioned release

## Skills Reference

### Setup

| Skill | Purpose |
|---|---|
| `/setup/bootstrap` | Create `.claude/artifacts/` directory structure and initial project files |
| `/setup/preferences` | Capture interaction preferences (stored in Claude Code memory) |

### Planning (Sequential Workflow)

Run these in order for a new project or major initiative:

| Step | Skill | Output |
|---|---|---|
| 1 | `/planning/describe-problem` | `.claude/artifacts/planning/problem-description.md` |
| 2 | `/planning/define-requirements` | `.claude/artifacts/planning/product-requirements.md` |
| 3 | `/planning/review-risks` | `.claude/artifacts/planning/risk-assumption-review.md` |
| 4 | `/planning/design-ux` (optional) | `.claude/artifacts/planning/ux-design-guide.md` |
| 5 | `/planning/technical-design` | `.claude/artifacts/planning/technical-design.md` |
| 6 | `/planning/create-backlog` | `.claude/artifacts/planning/product-backlog.md` |

### Implementation (Iterative Cycle)

| Skill | Purpose |
|---|---|
| `/implementation/pick-feature` | Select the next feature from the backlog |
| `/implementation/plan-feature` | Create a detailed implementation plan with Gherkin spec |
| `/implementation/review-plan` | Quality review of an existing plan (optional) |
| `/implementation/execute-plan` | Implement chunk by chunk with quality gates |
| `/implementation/validate` | Structured validation pass with the user (optional) |
| `/implementation/triage-backlog` | Reprioritize the backlog (optional) |

### Git & Release

| Skill | Purpose |
|---|---|
| `/git/merge-to-trunk` | Rebase + fast-forward merge (no merge commits) |
| `/release/prepare-release` | SemVer release with constellation codenames |

### Operations

| Skill | Purpose | Output |
|---|---|---|
| `/ops/triage-logs` | Turn alerts/logs into hypotheses + action plan | `.claude/artifacts/ops/YYYY-MM-DD_triage_<slug>.md` |
| `/ops/assess-risk` | Pre-deploy go/no-go risk assessment | `.claude/artifacts/ops/YYYY-MM-DD_risk_<slug>.md` |
| `/ops/review-incident` | Blameless incident review | `.claude/artifacts/ops/YYYY-MM-DD_incident_<slug>.md` |
| `/ops/root-cause` | Root cause analysis | `.claude/artifacts/ops/YYYY-MM-DD_rca_<slug>.md` |

## Artifact Structure

Created by `/setup/bootstrap` inside your project's `.claude/` directory:

```
.claude/
  skills/                  # The toolkit (copied from this repo)
  artifacts/               # Project documents (created by /setup/bootstrap)
    planning/              # Backlog, requirements, problem description, designs
      tasks/               # Feature implementation plans
    decisions/             # Decision log, open questions
    project/               # Project meta, release tracking
    ops/                   # Incident reviews, RCAs, risk assessments
```

Commit `skills/` and `artifacts/` to your repo so the team shares them. Add a `.claude/.gitignore` to exclude personal files:

```gitignore
# Personal settings (not shared)
settings.local.json

# Temporary plans (conversation-scoped)
plans/

# Claude Code memory (per-user, not project docs)
projects/
```

## Design Principles

- **Self-contained**: Each skill inlines all context needed to execute. No cross-file references.
- **Project-agnostic**: No assumptions about tech stack, framework, or existing file structure.
- **Convention over configuration**: Fixed `.claude/artifacts/` directory. No CLAUDE.md edits required.
- **Claude Code-native**: Leverages skills, memory, and Claude Code's built-in conversation management.
- **Trunk-based development**: Linear history, Conventional Commits, rebase + fast-forward only.

## Workflows

### Full Planning Cycle
```
/setup/bootstrap → /planning/describe-problem → /planning/define-requirements →
/planning/review-risks → /planning/design-ux → /planning/technical-design →
/planning/create-backlog
```

### Implementation Loop
```
/implementation/pick-feature → /implementation/plan-feature →
/implementation/execute-plan → /implementation/validate →
/implementation/triage-backlog → (repeat)
```

### Ship
```
/git/merge-to-trunk → /release/prepare-release
```

## Origins

This toolkit is a Claude Code-native port of the [Agentic Software Delivery Toolkit](https://github.com/leifericf/agentic-software-delivery-toolkit), which is tool-agnostic. This edition replaces the generic file-reference chains with Claude Code's native skill system.
