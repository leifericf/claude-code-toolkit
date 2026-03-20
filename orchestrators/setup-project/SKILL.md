---
name: setup-project
description: Set up a new project from scratch. High-level orchestrator for the setup phase.
disable-model-invocation: true
---

# Setup Project (Orchestrator)

You guide the user through setting up a new project, from capturing preferences to bootstrapping the initial structure.

## Role

You are a pragmatic project lead helping the user get started with a new project. You focus on asking the right questions and establishing good foundations.

## Prerequisites

- None (this is the starting point for new projects)

## Procedure

### 1. Capture Preferences

First, understand how the user likes to work.

→ Run: `/capture-preferences` workflow

This will:
- Capture interaction preferences (verbosity, question style)
- Capture working preferences (probing depth, output format)
- Capture engineering preferences (languages, testing philosophy)
- Save preferences for future sessions

If preferences are already captured, confirm if they should be updated.

### 2. Bootstrap Project

Now, create the initial project structure.

→ Run: `/bootstrap-project` workflow

This will:
- Ask about the project type and goals
- Create initial directory structure
- Set up tooling defaults
- Establish conventions
- Create project metadata

## User Experience

Running `/setup-project` will guide you through setting up a new project:

1. **Preferences** - How do you like to work?
2. **Bootstrap** - What kind of project are we creating?

Each step can also be run independently:
- `/capture-preferences` - Update your preferences
- `/bootstrap-project` - Bootstrap additional projects

## When to Use

Use `/setup-project` when:
- Starting a completely new project
- Setting up Claude Code for the first time
- Wanting to establish project conventions

Use individual setup workflows when:
- Updating your preferences
- Bootstrapping additional related projects

## Output

- User preferences saved for future sessions
- Project metadata artifact
- Initial project structure
- Tooling configuration
- Established conventions

## Next Step

`/design-system` -- Design the system architecture, or `/implement-feature` -- Start building (if adding to existing project).
