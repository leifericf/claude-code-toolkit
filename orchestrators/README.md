# Orchestrators

High-level entry points that you invoke directly via slash commands.

These orchestrators compose multiple workflows into complete end-to-end flows. Most of the time, you'll run an orchestrator and it will handle the entire process automatically.

## Available Orchestrators

### Core Workflow
- `/implement-feature` - Pick, plan, and implement a feature from selection to validation

### Design Workflow
- `/design-system` - Design a system from problem to backlog

### Incident Response
- `/incident-response` - Handle incidents from triage to follow-up

### Project Setup
- `/setup-project` - Set up a new project

### Git & Release
- `/merge-to-trunk` - Merge a feature branch to trunk
- `/prepare-release` - Prepare a versioned release

## When to Use Orchestrators vs. Workflows

**Use orchestrators** when:
- You want to accomplish a complete end-to-end workflow
- You want the system to handle all the steps automatically
- You're not sure which individual workflows to run

**Use workflows** (in `../workflows/`) when:
- You need fine-grained control over a specific step
- You want to run a standalone workflow (e.g., just elicit requirements)
- You're debugging or understanding a specific part of the process

## Architecture

```
Orchestrators (Layer 1)
    ↓ compose
Workflows (Layer 2)
    ↓ compose
Primitives (Layer 3)
```

See `../workflows/README.md` for composable workflows.
See `../primitives/README.md` for internal primitives.
