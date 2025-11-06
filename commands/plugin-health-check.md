---
description: Analyze all artifacts in the plugin, validate quality, and generate comprehensive health report with scores and improvement recommendations.
allowed-tools: Read, Bash, Grep, Glob
---

# Plugin Quality Dashboard

Comprehensive quality analysis for all Claude Code artifacts in the current plugin or project. Validates Skills, Commands, Subagents, and Hooks, providing aggregated quality scores and actionable recommendations.

## Objective

Perform plugin-wide health check by:
1. Discovering all artifacts (Skills, Commands, Subagents, Hooks)
2. Validating each artifact's YAML, structure, and quality
3. Running quality checks using validation framework
4. Aggregating scores across all artifacts
5. Identifying cross-artifact issues (conflicts, overlaps, gaps)
6. Generating comprehensive health report
7. Providing prioritized improvement recommendations

## Methodology

### Step 1: Artifact Discovery (2-5 min)

**Scan all artifact locations:**

```bash
# Find all Skills
find .claude/skills -name "SKILL.md" 2>/dev/null || true
find ~/.claude/skills -name "SKILL.md" 2>/dev/null || true

# Find all Commands
find .claude/commands -name "*.md" 2>/dev/null || true
find ~/.claude/commands -name "*.md" 2>/dev/null || true

# Find all Subagents
find .claude/agents -name "*.md" 2>/dev/null || true
find ~/.claude/agents -name "*.md" 2>/dev/null || true

# Check for Hooks in settings
test -f .claude/settings.local.json && echo "Settings file exists"
test -f ~/.claude/settings.json && echo "User settings exists"
```

**Categorize discovered artifacts:**
- Count by type (Skills, Commands, Subagents, Hooks)
- Identify project-level vs user-level
- Note any unusual locations or patterns

**Output of Step 1:**
```
Artifact Inventory:
- Skills: X project, Y user
- Commands: X project, Y user
- Subagents: X project, Y user
- Hooks: X configured
Total: N artifacts
```

---

### Step 2: Individual Artifact Validation (5-15 min)

**For each artifact, validate:**

#### Skills Validation

**Check list for each Skill:**

1. **YAML Syntax**
   ```bash
   python3 -c "
   import yaml
   with open('SKILL.md') as f:
       content = f.read().split('---')
       yaml.safe_load(content[1])
   " && echo "✅ Valid" || echo "❌ Invalid"
   ```

2. **Required Fields**
   - `name` present and valid
   - `description` present and ≤1024 chars

3. **Naming Convention**
   - Directory name matches YAML `name`
   - Lowercase with hyphens only

4. **Description Quality**
   - Contains action verbs (extract, analyze, test, etc.)
   - Includes trigger keywords
   - Mentions technologies/domains
   - Has "Use when/for" clause
   - Specific, not vague
   - 50-1024 characters

5. **Tool Access**
   - If `allowed-tools` present, uses comma-separated format
   - Tool names are valid

6. **Content Quality**
   - Has clear structure (headings, sections)
   - Includes methodology or workflow
   - Contains examples (3+ recommended)
   - No placeholder text (TODO, FIXME)

**Grading criteria:**
- **A (90-100%)**: Excellent quality, ready for production
- **B (80-89%)**: Good quality, minor improvements suggested
- **C (70-79%)**: Acceptable, several improvements needed
- **D (60-69%)**: Below standard, significant issues
- **F (<60%)**: Fails validation, cannot be used reliably

#### Commands Validation

**Check list for each Command:**

1. **File Extension**
   - Must be `.md` file

2. **YAML Frontmatter** (if present)
   - Valid YAML syntax
   - `description` field recommended
   - `argument-hint` if accepts arguments
   - `allowed-tools` if restricted

3. **Naming Convention**
   - Lowercase with hyphens
   - No spaces or special characters

4. **Content Quality**
   - Clear prompt instructions
   - Proper argument substitution ($ARGUMENTS, $1, $2)
   - Structured with sections
   - Examples if complex

5. **Tool Safety**
   - Destructive ops have `disable-model-invocation: true`
   - Appropriate tool restrictions

#### Subagents Validation

**Check list for each Subagent:**

1. **YAML Configuration**
   - Valid YAML syntax
   - Required: `name`, `description`
   - Optional: `tools`, `model`

2. **Naming Convention**
   - Lowercase with hyphens
   - Descriptive of specialization

3. **Description Quality**
   - Specific about capabilities
   - Lists use cases for delegation
   - Clear domain/specialization

4. **Tool Access**
   - Appropriate for task
   - Follows principle of least privilege

5. **System Prompt Quality**
   - Clear role definition
   - Step-by-step methodology
   - Specified output format
   - Quality guidelines
   - Examples (recommended)

6. **Model Selection**
   - Justified for task complexity
   - Not over/under-powered

#### Hooks Validation

**Check list for each Hook:**

1. **Configuration Valid**
   - Proper JSON syntax in settings
   - Event type specified correctly
   - Command/script exists

2. **Security Review**
   - ⚠️ CRITICAL: All hooks require security review
   - No arbitrary code execution
   - Proper variable quoting
   - Input validation present
   - Error handling exists

3. **Safety Checks**
   - Non-destructive or has confirmations
   - Handles failures gracefully
   - Logs actions appropriately

---

### Step 3: Quality Scoring (5-10 min)

**Apply quality framework to each artifact:**

**Quality Formula:** `Q = 0.40·R + 0.30·C + 0.20·S + 0.10·E`

Where:
- **R (Relevance)**: How well does it serve its purpose?
  - Description clarity
  - Activation/trigger reliability
  - Appropriate scope

- **C (Completeness)**: Is everything present?
  - Required fields
  - Documentation
  - Examples
  - Error handling

- **S (Specification)**: Does it follow standards?
  - YAML syntax
  - Naming conventions
  - Tool access patterns
  - File structure

- **E (Efficiency)**: Is it optimized?
  - Tool restrictions appropriate
  - No redundant content
  - Clear, concise prompts

**Score each component 0.0-1.0, then calculate Q:**

**Example scoring:**
```
Skill: pdf-processor
- Relevance (R): 0.95 (excellent description, clear triggers)
- Completeness (C): 0.85 (has examples, missing some edge cases)
- Specification (S): 1.0 (perfect YAML, naming)
- Efficiency (E): 0.90 (appropriate tools)

Q = 0.40(0.95) + 0.30(0.85) + 0.20(1.0) + 0.10(0.90)
Q = 0.38 + 0.255 + 0.20 + 0.09
Q = 0.925 (93%) → Grade A
```

**Calculate for all artifacts and track:**
- Individual scores by artifact
- Average score by type (Skills avg, Commands avg, etc.)
- Overall plugin score

---

### Step 4: Cross-Artifact Analysis (5-10 min)

**Check for plugin-wide issues:**

#### Conflict Detection

**Name conflicts:**
```bash
# Check for duplicate names across artifact types
find .claude/ -name "SKILL.md" -o -name "*.md" -o -name "*.yaml" | \
  xargs basename -s .md | sort | uniq -d
```

**Overlapping responsibilities:**
- Multiple Skills with similar descriptions
- Commands and Skills with overlapping purposes
- Subagents with duplicate specializations

#### Gap Analysis

**Check coverage:**
- Are common workflows covered?
- Missing artifact types? (e.g., have Skills but no Commands)
- Obvious pain points not addressed?

#### Consistency Checks

- **Naming patterns**: Do all artifacts follow same conventions?
- **Tool access patterns**: Are restrictions consistent?
- **Documentation**: Are all project artifacts documented?
- **Quality variance**: Large quality differences between artifacts?

#### Integration Issues

- **Orphaned files**: Supporting files with no parent artifact
- **Broken references**: Skills referencing non-existent files
- **Missing dependencies**: Tools or scripts referenced but not present

---

### Step 5: Health Report Generation (10-15 min)

**Generate comprehensive report:**

## Plugin Health Report

**Generated:** [Date]
**Plugin:** [Plugin name or "Current project"]

---

### Executive Summary

**Overall Health:** [Grade A-F] ([Score]%)

**Artifact Count:**
- Skills: X (X project, X user)
- Commands: X (X project, X user)
- Subagents: X (X project, X user)
- Hooks: X configured

**Quality Distribution:**
- Grade A (≥90%): X artifacts
- Grade B (80-89%): X artifacts
- Grade C (70-79%): X artifacts
- Grade D (60-69%): X artifacts
- Grade F (<60%): X artifacts

**Top Issues:**
1. [Most critical issue across artifacts]
2. [Second most critical issue]
3. [Third most critical issue]

---

### Artifact Scores

#### Skills

| Name | Location | Grade | Score | Status |
|------|----------|-------|-------|--------|
| skill-1 | project | A | 95% | ✅ Excellent |
| skill-2 | user | B | 85% | ⚠️ Minor improvements |
| skill-3 | project | F | 45% | ❌ Critical issues |

**Average Skill Score:** X% (Grade: X)

#### Commands

| Name | Location | Grade | Score | Status |
|------|----------|-------|-------|--------|
| command-1 | project | A | 92% | ✅ Excellent |
| command-2 | user | C | 75% | ⚠️ Needs work |

**Average Command Score:** X% (Grade: X)

#### Subagents

| Name | Location | Grade | Score | Status |
|------|----------|-------|-------|--------|
| agent-1 | project | B | 88% | ✅ Good |

**Average Subagent Score:** X% (Grade: X)

#### Hooks

| Event | Status | Security Review | Issues |
|-------|--------|-----------------|--------|
| on_tool_call_finish | ✅ | ⚠️ Needs review | 2 warnings |

**Hooks Status:** X configured, X need security review

---

### Detailed Findings

#### Critical Issues (Fix Immediately)

**[Artifact Name] ([Type])** - Grade F (X%)
- **Issue:** [Specific problem]
- **Impact:** [Why this is critical]
- **Fix:** [Specific remediation steps]

[Repeat for all critical issues]

#### Warnings (Address Soon)

**[Artifact Name] ([Type])** - Grade C (X%)
- **Issue:** [Specific problem]
- **Impact:** [Why this needs attention]
- **Fix:** [Specific remediation steps]

[Repeat for all warnings]

#### Recommendations (Improvements)

**[Artifact Name] ([Type])** - Grade B (X%)
- **Suggestion:** [Improvement opportunity]
- **Benefit:** [Why this would help]
- **Implementation:** [How to implement]

[Repeat for all recommendations]

---

### Cross-Artifact Issues

#### Conflicts Detected

- **Name conflict:** [artifact-1] and [artifact-2] have overlapping names
- **Responsibility overlap:** [artifact-3] and [artifact-4] have similar purposes

#### Gaps Identified

- **Missing coverage:** No artifact handles [common workflow]
- **Type gap:** Have Skills but no Commands for explicit invocation
- **Specialization gap:** No Subagent for [complex task type]

#### Consistency Issues

- **Naming:** [X] artifacts don't follow naming conventions
- **Tool access:** Inconsistent patterns across artifacts
- **Documentation:** [X] project artifacts not documented in README

---

### Quality Trends

**Strengths:**
- [What the plugin does well]
- [Consistent quality area]
- [Notable achievements]

**Weaknesses:**
- [Common quality issue across artifacts]
- [Area needing improvement]
- [Missing capabilities]

**Improvement Opportunities:**
- [Specific opportunity 1]
- [Specific opportunity 2]
- [Specific opportunity 3]

---

### Action Plan

**Priority 1: Critical Fixes (Do Immediately)**
1. Fix [artifact] YAML syntax error
2. Add security review for [hook]
3. Resolve name conflict between [artifact-1] and [artifact-2]

**Priority 2: Important Improvements (Do Soon)**
1. Improve [artifact] description for better activation
2. Add examples to [artifact]
3. Document [artifact] in README

**Priority 3: Enhancements (Nice to Have)**
1. Add [missing artifact type] for [workflow]
2. Refine [artifact] methodology
3. Create complementary [artifact] for [use case]

---

### Validation Commands

**Re-validate specific artifact:**
```bash
# Use artifact-validator Skill
"Validate the [artifact-name] [type]"
```

**Re-run full health check:**
```bash
/plugin-health-check
```

---

### Summary Statistics

**Total Artifacts:** X
**Average Quality Score:** X% (Grade: X)
**Artifacts Needing Attention:** X
**Critical Issues:** X
**Warnings:** X
**Recommendations:** X

**Health Trend:** [Improving/Stable/Declining]

---

## Report Formats

### Console Output (Default)

**Quick summary with tables for immediate review**

### Detailed Report

**Comprehensive analysis with all findings**

### JSON Export (Optional)

```json
{
  "generated": "2025-11-06T12:00:00Z",
  "overall_score": 0.87,
  "overall_grade": "B",
  "artifact_count": {
    "skills": 5,
    "commands": 3,
    "subagents": 2,
    "hooks": 1
  },
  "scores": {
    "skills": [
      {
        "name": "skill-1",
        "score": 0.95,
        "grade": "A",
        "issues": []
      }
    ]
  },
  "issues": {
    "critical": 1,
    "warnings": 3,
    "recommendations": 5
  }
}
```

---

## Usage Examples

### Example 1: First-Time Health Check

```
User: /plugin-health-check

Output:
Discovering artifacts...
Found 8 total artifacts (5 Skills, 2 Commands, 1 Subagent)

Validating artifacts...
✅ skill-builder (95%) - Grade A
✅ artifact-advisor (92%) - Grade A
⚠️ claude-expert (78%) - Grade C - Missing examples
❌ old-skill (45%) - Grade F - Invalid YAML

Overall Health: B (84%)
Critical Issues: 1
Warnings: 1

[Full report follows...]
```

### Example 2: After Improvements

```
User: /plugin-health-check

Output:
Discovering artifacts...
Found 8 total artifacts

Validating artifacts...
All artifacts validated successfully!

Overall Health: A (93%)
No critical issues found.

[Summary report follows...]
```

### Example 3: Cross-Artifact Issues

```
User: /plugin-health-check

Output:
...
Cross-Artifact Issues:
⚠️ Name conflict: 'review' exists as both Skill and Command
⚠️ Overlap: 'api-tester' (Skill) and 'test-api' (Command) have similar purposes
⚠️ Gap: No Subagent for complex analysis tasks

[Recommendations follow...]
```

---

## Best Practices

✅ **DO:**
- Run health checks before deployments
- Address critical issues immediately
- Track quality trends over time
- Use reports to guide improvements
- Share reports with team (project plugins)
- Re-validate after fixes
- Document resolution of issues

❌ **DON'T:**
- Ignore critical issues (F grades)
- Deploy plugins with security warnings (Hooks)
- Let quality drift over time
- Skip validation after changes
- Ignore cross-artifact conflicts
- Assume validation once is enough

---

## Integration with Other Tools

**After health check:**

1. **Fix critical artifacts:**
   - Use `artifact-validator` for detailed analysis
   - Use `skill-builder`, `/build-command`, `/build-subagent` to rebuild

2. **Add missing artifacts:**
   - Use `/suggest-artifacts` to identify gaps
   - Use `artifact-advisor` to choose right type

3. **Research best practices:**
   - Use `claude-researcher` for official guidance
   - Use `claude-expert` for specifications

---

## Success Criteria

A healthy plugin has:
- ✅ Overall score ≥80% (Grade B or better)
- ✅ No critical issues (no F grades)
- ✅ All hooks security reviewed
- ✅ No name conflicts
- ✅ Consistent naming conventions
- ✅ All project artifacts documented
- ✅ Appropriate tool restrictions
- ✅ Clear coverage of intended workflows
- ✅ No major gaps or overlaps

---

## Troubleshooting

### Issue: Many artifacts fail validation

**Cause:** May be using outdated conventions

**Solution:**
1. Review specifications in `claude-expert`
2. Update artifacts to current standards
3. Use builder tools to regenerate

### Issue: Can't find artifacts

**Cause:** Non-standard locations

**Solution:**
1. Check `.claude/` directory structure
2. Verify files are in correct locations:
   - Skills: `.claude/skills/*/SKILL.md`
   - Commands: `.claude/commands/*.md`
   - Subagents: `.claude/agents/*.md`

### Issue: All hooks show security warnings

**Cause:** Hooks require manual security review

**Solution:**
1. Review each hook's script
2. Verify no code injection vulnerabilities
3. Check variable quoting
4. Document security review completion

---

## Maintenance

**Run plugin health checks:**
- Before every plugin release
- After adding/modifying artifacts
- Monthly for active development
- Before major deployments
- When troubleshooting issues

**Track quality over time:**
- Keep historical reports
- Monitor trends (improving/declining)
- Set quality goals (e.g., "All artifacts Grade B+")
- Celebrate improvements

---

**This command provides comprehensive plugin health visibility, enabling proactive quality management and continuous improvement of your Claude Code artifacts!**
