---
description: Sync README.md with current project state and artifact inventory
allowed-tools: Read, Write, Glob, Grep
---

# README Synchronization

Update the README.md file to accurately reflect the current state of the Ouroboros plugin, including all Skills, Commands, Subagents, and artifacts.

## Objective

Ensure README.md is completely synchronized with:
1. **Current artifact inventory** (Skills, Commands, Subagents, Hooks)
2. **Artifact descriptions** from their SKILL.md or command files
3. **Directory structure** reflecting actual files
4. **Version information** and recent changes
5. **Examples** that match current artifacts
6. **Feature counts** and statistics

## Synchronization Steps

### Step 1: Inventory Current Artifacts

Discover all artifacts in the project:

**Skills:**
```bash
find skills -name "SKILL.md" -type f
```

For each skill, extract:
- Skill name (directory name)
- Description (from YAML frontmatter)
- Purpose and activation triggers

**Commands:**
```bash
find commands -name "*.md" -type f
```

For each command, extract:
- Command name (filename without .md)
- Description (from YAML frontmatter or first heading)
- Arguments and usage

**Subagents:**
```bash
find subagents -name "SUBAGENT.md" -type f 2>/dev/null || echo "No subagents directory"
```

**Hooks:**
Check for Hook documentation in README or separate files.

### Step 2: Update Artifact Tables

Update these README sections with current data:

**"Available Tools" section:**
- Skills table with activation triggers
- Commands table with purposes
- Accurate counts and descriptions

**"Architecture" section:**
- Directory structure tree
- File listings that match reality

**"Examples" section:**
- Ensure examples reference existing artifacts
- Remove references to deleted artifacts
- Add examples for new artifacts

### Step 3: Verify Accuracy

Cross-check that:
- All mentioned artifacts actually exist
- No artifacts are missing from documentation
- Descriptions match source files
- Counts are accurate (e.g., "9 Skills", "6 Commands")
- Directory paths are correct

### Step 4: Update Version Information

If there's a changelog or version section:
- Update with recent additions
- Note deprecated or removed artifacts
- Highlight new features

### Step 5: Write Updated README

Write the synchronized README.md with:
- Current artifact inventory
- Accurate descriptions
- Correct examples
- Updated statistics
- Current directory structure

## What to Preserve

**Keep unchanged:**
- Overall structure and organization
- Introduction and "What is This?" sections
- Philosophy and "Why Ouroboros?" explanation
- Best practices sections
- Troubleshooting guides
- FAQ section
- License and credits

**Update only:**
- Artifact counts and listings
- Descriptions (sync from source files)
- Directory structure
- Examples that reference artifacts
- Statistics and metrics
- Version/changelog info

## Quality Checks

Before finalizing:

✅ **Accuracy:**
- [ ] Every artifact in README exists in codebase
- [ ] Every artifact in codebase appears in README
- [ ] Descriptions match source files
- [ ] Counts are correct

✅ **Completeness:**
- [ ] All Skills documented
- [ ] All Commands documented
- [ ] All Subagents documented
- [ ] Directory structure accurate

✅ **Consistency:**
- [ ] Naming conventions match
- [ ] Examples use real artifacts
- [ ] Links point to existing files

✅ **Clarity:**
- [ ] Tables are well-formatted
- [ ] Examples are clear
- [ ] No broken references

## Output Format

After synchronization, provide a summary:

```
README Synchronization Complete ✅

Changes made:
- Updated artifact inventory (9 Skills, 6 Commands)
- Synced descriptions for 3 artifacts
- Updated directory structure
- Fixed 2 broken references
- Added example for [new artifact]

Current state:
- Skills: [count]
- Commands: [count]
- Subagents: [count]
- Total artifacts: [count]

All artifacts verified and documented.
```

## Important Notes

- **Do not change** the README's overall structure or philosophy sections
- **Only sync** factual information about artifacts
- **Verify** all artifacts exist before documenting them
- **Extract** descriptions from actual artifact files (authoritative source)
- **Maintain** the README's friendly, helpful tone

---

**This command is safe** - it only updates documentation to match reality. It does not modify any artifacts themselves.
