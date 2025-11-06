# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

The **Ouroboros Plugin** helps you create and manage Claude Code artifacts using Claude Code artifacts itself - a meta/recursive system symbolized by the serpent eating its own tail.

**What it does:** Provides expert guidance for building Skills, Commands, Subagents, and Hooks through specialized skills and commands that handle workflow analysis, decision-making, creation workflows, validation, and documentation research.

## Architecture

### Skills and Commands

Five specialized skills and one command work together:

**Skills (automatic activation):**
1. **claude-expert** - Knowledge repository (specifications, workflows, decision frameworks)
2. **artifact-advisor** - Helps choose the right artifact type
3. **skill-builder** - Guides Skill creation workflow
4. **artifact-validator** - Quality validation and grading
5. **claude-researcher** - Fetches official Claude Code documentation

**Commands (explicit invocation):**
1. **/suggest-artifacts** - Analyzes workflows and suggests what artifacts to build
2. **/skills** - Manage Skills (create, update, delete, list)
3. **/commands** - Manage Commands (create, update, delete, list)
4. **/agents** - Manage Subagents (create, update, delete, list)
5. **/hooks** - Manage Hooks (create, update, delete, list)

### Key Pattern: Progressive Disclosure

Skills use a main `SKILL.md` file plus supporting files loaded only when needed, keeping context usage efficient.

### Directory Structure

```
ouroboros/
├── .claude-plugin/
│   └── plugin.json           # Plugin metadata
├── .claude/
│   └── settings.local.json   # Local permissions
├── commands/
│   ├── suggest-artifacts.md  # Workflow analysis command
│   ├── skills.md             # CRUD operations for Skills
│   ├── commands.md           # CRUD operations for Commands
│   ├── agents.md             # CRUD operations for Subagents
│   ├── hooks.md              # CRUD operations for Hooks
│   └── plugin-health-check.md
└── skills/
    ├── artifact-advisor/
    ├── artifact-validator/
    ├── claude-expert/        # Largest: contains specs, workflows, comparisons
    ├── claude-researcher/
    └── skill-builder/
```

## How to Use

### Determining What to Build
- **Start here:** Run `/suggest-artifacts` to analyze your workflow and get artifact suggestions
- Optional: Focus on specific domain with `/suggest-artifacts <domain>` (e.g., `/suggest-artifacts testing`)
- Command analyzes your work patterns and recommends 2-4 prioritized artifacts

### Managing Artifacts (CRUD Operations)

Use the new management commands with CRUD operations:

**Skills:**
- `/skills create my-skill` - Create new Skill
- `/skills my-skill` - Smart default: create if not exists, update if exists
- `/skills update my-skill` - Update existing Skill
- `/skills delete my-skill` - Delete Skill
- `/skills list` - List all Skills

**Commands:**
- `/commands create deploy "Deploy to environment"` - Create new Command with description
- `/commands deploy` - Smart default: create if not exists, update if exists
- `/commands update deploy` - Update existing Command
- `/commands delete deploy` - Delete Command
- `/commands list` - List all Commands

**Subagents:**
- `/agents create code-reviewer` - Create new Subagent
- `/agents code-reviewer` - Smart default: create if not exists, update if exists
- `/agents update code-reviewer` - Update existing Subagent
- `/agents delete code-reviewer` - Delete Subagent
- `/agents list` - List all Subagents

**Hooks:**
- `/hooks create pre-commit` - Create new Hook (requires security review)
- `/hooks pre-commit` - Smart default: create if not exists, update if exists
- `/hooks update pre-commit` - Update existing Hook
- `/hooks delete pre-commit` - Delete Hook
- `/hooks list` - List all Hooks

**Smart Default Behavior:** When you use just the artifact name (e.g., `/skills my-skill`), the command automatically detects if the artifact exists and updates it, or creates it if it doesn't exist.

### Skill-Based Assistance

- Ask for help creating artifacts → `skill-builder` activates
- Unsure which artifact type → `artifact-advisor` activates
- Need specifications → `claude-expert` activates

### Validating Work
- Before deployment → use `artifact-validator`

### Research
- Questions about Claude Code features → `claude-researcher` fetches official docs

### Multi-Skill Activation
Claude can activate multiple skills simultaneously (e.g., `skill-builder` + `claude-expert` together).

## Important Concepts

**Artifact Types:**
- Skills (automatic activation)
- Commands (user-triggered)
- Subagents (delegated tasks)
- Hooks (event-driven, requires security review)

**Details in:** `skills/claude-expert/comparison-matrix.md`

**Quality Framework:** Artifacts are validated using a weighted scoring system (details in `artifact-validator`).

## References

All detailed information is in the skill files under `skills/` directory. Each SKILL.md contains complete guidance for its domain.

Official Claude Code documentation: https://code.claude.com/docs/
