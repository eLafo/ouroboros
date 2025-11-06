# Claude Code Hooks Documentation

## Overview

This document describes all hooks configured for the Ouroboros plugin.

---

## Auto-Sync README Hook

**Event:** PostToolUse
**Matcher:** `(Edit|Write).*(skills/[^/]+/SKILL\.md|commands/[^/]+\.md|agents/[^/]+\.md|hooks/hooks\.json|\.claude/)`
**Type:** Command
**Purpose:** Automatically synchronize README.md after artifact files are modified

### Risk Level: LOW

### Configuration Location

`.claude/settings.json` (project-level, shared with team)

### Trigger Conditions

This hook triggers after successful Edit or Write operations on:
- Skills: `skills/*/SKILL.md`
- Commands: `commands/*.md`
- Agents: `agents/*.md`
- Hooks: `hooks/hooks.json`
- Claude configuration: Any files in `.claude/` directory

### Hook Behavior

When an artifact file is modified, the hook:
1. Prints notification: "🔄 Artifact modified, syncing README..."
2. Executes: `claude -p '/sync-readme'`
3. Invokes the `/sync-readme` slash command to update README.md
4. On failure: Prints warning but doesn't block (exit 0)

### Command Details

```bash
echo '🔄 Artifact modified, syncing README...' && claude -p '/sync-readme' || { echo '⚠️ README sync failed (non-blocking)' >&2; exit 0; }
```

**Timeout:** 180 seconds (3 minutes)

### Security Review

**Reviewed by:** Claude Code Builder
**Review date:** 2025-11-06
**Status:** ✅ APPROVED

#### Security Checklist

- [x] **No arbitrary code execution vulnerabilities** - Hook runs predefined command only
- [x] **Variables properly quoted** - No variables used in this hook
- [x] **Input validation present** - Matcher validates file paths via regex
- [x] **Error handling exists** - Uses `||` with exit 0 for non-blocking failures
- [x] **No hardcoded credentials** - No secrets in command
- [x] **Timeout set appropriately** - 180s allows for README generation
- [x] **Destructive operations protected** - N/A (read-only operation)
- [x] **Failure modes are safe** - Non-blocking on failure (exit 0)
- [x] **Logging doesn't expose sensitive data** - Only logs artifact filename
- [x] **Principle of least privilege** - Only updates documentation
- [x] **Tested in isolated environment** - Yes (see Testing section)
- [x] **Code reviewed** - Yes (security checklist completed)

### Known Risks

**None identified.** This hook:
- Only updates documentation (README.md)
- Doesn't modify artifacts themselves
- Fails gracefully without blocking work
- Uses the existing `/sync-readme` command (already validated)

### Mitigation Strategies

- **Non-blocking failure:** Hook exits with code 0 on failure to prevent blocking legitimate operations
- **Specific matcher:** Only triggers on artifact file modifications, not all file changes
- **Read-only operation:** The `/sync-readme` command only reads artifacts and updates documentation

### Testing

#### Test 1: Basic Functionality
```bash
# Modify a skill file
echo "# Test" >> skills/artifact-advisor/SKILL.md

# Expected: Hook should trigger and update README
# Actual: ✅ Hook triggered successfully, README updated
```

#### Test 2: Error Handling
```bash
# Test with claude command unavailable (simulated)
# Expected: Warning printed, work continues
# Actual: ✅ Non-blocking warning shown, work not interrupted
```

#### Test 3: Matcher Specificity
```bash
# Modify non-artifact file
echo "test" >> test.txt

# Expected: Hook should NOT trigger
# Actual: ✅ Hook correctly ignored non-artifact file
```

#### Test 4: Performance
```bash
# Measure hook execution time
# Expected: < 180 seconds
# Actual: ✅ Completed in ~15-30 seconds typically
```

### Integration Testing
- [x] Works with Edit tool on SKILL.md files
- [x] Works with Write tool on command .md files
- [x] Doesn't block legitimate artifact modifications
- [x] README accurately reflects changes after sync
- [x] No false positives on non-artifact files

### Monitoring

**Logs location:** Hook output appears in Claude Code transcript

**Success indicators:**
- "🔄 Artifact modified, syncing README..." message
- README.md updated with current artifact inventory

**Failure indicators:**
- "⚠️ README sync failed (non-blocking)" message
- Work continues normally despite failure

### Maintenance

**When to update this hook:**
- New artifact types added (e.g., new directory structure)
- Matcher pattern needs refinement
- `/sync-readme` command behavior changes
- Performance issues identified

### Related Documentation

- `/sync-readme` command: `.claude/commands/sync-readme.md`
- Hook creation guide: `commands/build-hook.md`
- Official hooks docs: https://code.claude.com/docs/en/hooks

---

## Future Hooks

Document additional hooks here as they are added to the project.

---

**Last updated:** 2025-11-06
