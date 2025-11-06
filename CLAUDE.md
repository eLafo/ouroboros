# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

The **Ouroboros Plugin** helps you create and manage Claude Code artifacts using Claude Code artifacts itself - a meta/recursive system symbolized by the serpent eating its own tail.

**What it does:** Provides expert guidance for building Skills, Commands, Subagents, and Hooks through specialized skills that handle decision-making, creation workflows, validation, and documentation research.

## Architecture

### Multi-Skill System

Five specialized skills work together:

1. **claude-expert** - Knowledge repository (specifications, workflows, decision frameworks)
2. **artifact-advisor** - Helps choose the right artifact type
3. **skill-builder** - Guides Skill creation workflow
4. **artifact-validator** - Quality validation and grading
5. **claude-researcher** - Fetches official Claude Code documentation

### Key Pattern: Progressive Disclosure

Skills use a main `SKILL.md` file plus supporting files loaded only when needed, keeping context usage efficient.

### Directory Structure

```
claude-manager-plugin/
├── .claude-plugin/
│   └── plugin.json           # Plugin metadata
├── .claude/
│   └── settings.local.json   # Local permissions
└── skills/
    ├── artifact-advisor/
    ├── artifact-validator/
    ├── claude-expert/        # Largest: contains specs, workflows, comparisons
    ├── claude-researcher/
    └── skill-builder/
```

## How to Use

### Creating Artifacts
- Ask for help creating a Skill → `skill-builder` activates
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
