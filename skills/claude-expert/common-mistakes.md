
# Common Mistakes and Anti-Patterns

## Document Purpose

This context captures **common mistakes, anti-patterns, and lessons learned** from creating Claude Code artifacts. Use this to avoid known pitfalls and troubleshoot issues.

**Target audience:** All developers working with Claude Code artifacts
**Usage:** Review before creating artifacts, consult when troubleshooting

---

## Skills: Common Mistakes

### Mistake 1: Vague Skill Descriptions

**Problem:**
```yaml
description: Helps with documents
```

**Why it fails:** Too generic, Claude can't determine when to activate

**Fix:**
```yaml
description: Extract text, images, and tables from PDFs. Fill PDF forms, merge documents, convert formats. Use when working with PDF files or document processing.
```

**Prevention:** Include 3+ specific technologies, 5+ action verbs, concrete use cases

---

### Mistake 2: Skill Never Activates

**Symptoms:** User mentions PDF files, but pdf-processor Skill doesn't activate

**Common causes:**
- Description lacks keywords user naturally mentions
- Description is too narrow or too broad
- YAML syntax error prevents Skill from loading

**Diagnosis:**
```bash
# Check if Skill file is valid
python3 -c "import yaml; yaml.safe_load(open('.claude/skills/pdf-processor/SKILL.md').read().split('---')[1])"

# Test if description contains user keywords
grep -i "pdf\|document" .claude/skills/pdf-processor/SKILL.md
```

**Fix:**
1. Add trigger keywords user naturally mentions
2. Test with explicit requests: "Process this PDF"
3. Restart Claude Code session to reload Skills

**Lesson:** Test activation with 3-5 different phrasings before deployment

---

### Mistake 3: Directory Name Doesn't Match YAML Name

**Problem:**
```
Directory: .claude/skills/pdf-processor/
YAML: name: pdf_processor
```

**Result:** Skill doesn't load or conflicts occur

**Fix:**
```yaml
# Match directory name exactly
name: pdf-processor
```

**Prevention:** Always verify `name` field matches directory name exactly

---

### Mistake 4: Using Array Syntax for allowed-tools

**Wrong:**
```yaml
allowed-tools:
  - Read
  - Write
```

**Right:**
```yaml
allowed-tools: Read, Write
```

**Lesson:** Use comma-separated format, not YAML arrays

---

### Mistake 5: Skill for Explicit User Actions

**Anti-pattern:**
```yaml
name: deploy-to-production
description: Deploy application to production
```

**Why wrong:** Deployment is deliberate, high-stakes action requiring explicit user trigger

**Right approach:** Use slash command instead
```markdown
---
description: Deploy to production
disable-model-invocation: true
---
/deploy-production
```

**Rule:** If user should explicitly trigger it, use Command not Skill

---

## Commands: Common Mistakes

### Mistake 1: Missing .md Extension

**Problem:**
```
.claude/commands/review-pr  # No extension
```

**Result:** Command not recognized

**Fix:**
```
.claude/commands/review-pr.md
```

**Prevention:** Always include `.md` extension

---

### Mistake 2: Arguments Not Substituted

**Problem:**
User invokes: `/test-file src/app.js`
Command shows: `Testing file: $1` (literally)

**Cause:** No arguments provided in testing, or syntax error

**Fix:**
- Ensure command actually receives arguments
- Check syntax: `$1` not `${1}` for basic substitution
- Test invocation: `/test-file actual-file.js`

---

### Mistake 3: Using Commands for Automatic Behavior

**Anti-pattern:**
```markdown
---
description: Check for security vulnerabilities
---

Run npm audit after every dependency change
```

**Why wrong:** User must manually run `/security-check` after changes

**Right approach:** Use Hook for automatic execution
```json
{
  "hooks": {
    "PostToolUse": [
      {"matcher": "Write.*package\\.json", "hooks": [{"command": "npm audit"}]}
    ]
  }
}
```

**Rule:** Commands are manual, Hooks are automatic

---

### Mistake 4: No argument-hint for Multi-Arg Commands

**Problem:**
```yaml
---
description: Deploy application
---

Deploy to $1 using branch $2
```

**User confusion:** What are $1 and $2?

**Fix:**
```yaml
---
description: Deploy application
argument-hint: <environment> <branch>
---
```

**Prevention:** Always add `argument-hint` when using arguments

---

## Subagents: Common Mistakes

### Mistake 1: Subagent for Simple Tasks

**Anti-pattern:**
```yaml
---
name: file-reader
description: Read files
tools: Read
---

Read the requested file.
```

**Why wrong:** Reading files doesn't need isolated context or specialized agent

**Right approach:** Main agent uses Read tool directly

**Rule:** Subagents are for complex, focused sub-tasks requiring isolation

---

### Mistake 2: Vague System Prompt

**Problem:**
```markdown
You are a code reviewer. Review code.
```

**Why fails:** No methodology, no output format, no guidelines

**Fix:**
```markdown
# Code Review Specialist

You are a code review specialist focused on quality, security, and maintainability.

## Review Process
1. Run git diff to see changes
2. Analyze for security vulnerabilities
3. Evaluate code quality
4. Provide structured feedback

## Output Format
### Summary
[2-3 sentences]

### Issues
[Bulleted list with file:line references]

### Recommendations
[Prioritized list]
```

**Prevention:** Include methodology + output format + guidelines in every subagent

---

### Mistake 3: Tool Restrictions Too Tight

**Problem:**
```yaml
tools: Read
```

**Subagent needs:** Bash (for git diff), Grep (for searching)

**Result:** Subagent can't complete task

**Fix:**
```yaml
tools: Read, Grep, Bash
```

**Or:** Omit `tools` field to inherit all tools

**Prevention:** Test subagent workflow end-to-end before restricting tools

---

### Mistake 4: Wrong Model for Task Complexity

**Problem:**
```yaml
model: haiku
```

**Task:** Complex security audit requiring deep reasoning

**Result:** Superficial analysis, missed issues

**Fix:**
```yaml
model: sonnet  # Or opus for very complex tasks
```

**Rule:**
- Simple/fast tasks → haiku
- Standard tasks → sonnet
- Complex reasoning → opus

---

## Hooks: Common Mistakes

### Mistake 1: Unquoted Variables (CRITICAL SECURITY ISSUE)

**DANGEROUS:**
```bash
rm -rf $FILE_PATH
```

**Input:** `"; rm -rf /"`
**Result:** Executes `rm -rf ""; rm -rf /` → DELETES SYSTEM

**SAFE:**
```bash
rm -rf "$FILE_PATH"
```

**Prevention:** ALWAYS quote all variables: `"$VAR"`

---

### Mistake 2: No Input Validation

**DANGEROUS:**
```bash
cat "$FILE_PATH"
```

**Input:** `../../../etc/passwd`
**Result:** Reads sensitive system files

**SAFE:**
```bash
if [[ "$FILE_PATH" != "$CLAUDE_PROJECT_DIR"* ]]; then
    echo "Path outside project" >&2
    exit 2
fi
cat "$FILE_PATH"
```

**Prevention:** Validate all inputs, especially paths

---

### Mistake 3: Accessing Credentials

**DANGEROUS:**
```bash
if grep -q "API_KEY" .env; then
    echo "Found API key in .env"
fi
```

**Problem:** Hook accesses `.env` file, could log sensitive data

**SAFE:**
```bash
# Don't access credential files at all
# Use environment variables instead
if [ -z "$API_KEY" ]; then
    echo "API_KEY not set"
fi
```

**Prevention:** Never access `.env`, `.git/`, credentials files

---

### Mistake 4: No Timeout

**Problem:**
```json
{"command": "npm install"}
```

**Result:** Could hang forever if npm registry is down

**Fix:**
```json
{"command": "npm install", "timeout": 120}
```

**Prevention:** Always set timeout (< 300s typical)

---

### Mistake 5: Using Hooks for Reasoning

**Anti-pattern:**
```json
{
  "hooks": {
    "PostToolUse": [
      {"matcher": "Write", "hooks": [{"command": "analyze-code-quality.sh"}]}
    ]
  }
}
```

**Why wrong:** Code quality analysis requires reasoning, not just validation

**Right approach:** Use Subagent for analysis
```yaml
---
name: code-quality-analyzer
description: Analyzes code quality and suggests improvements
---
```

**Rule:** Hooks are for quick validation/integration, not reasoning

---

### Mistake 6: Missing Security Review

**Problem:** Developer creates hook, commits directly without review

**Result:** Potential security vulnerabilities in production

**Mandatory:**
```
1. Create hook
2. Get peer security review
3. Test in isolated environment
4. Document security considerations
5. Deploy gradually
```

**Prevention:** NO hook goes to production without peer review

---

## Cross-Cutting Mistakes

### Mistake 1: Wrong Artifact Type

**Symptoms:** Artifact doesn't work as expected

**Common confusions:**

| User wants | Wrong choice | Right choice |
|-----------|--------------|--------------|
| Claude automatically helps with PDFs | Command | Skill |
| User explicitly runs tests | Skill | Command |
| Validate before committing | Skill/Command | Hook |
| Complex code review sub-task | Skill | Subagent |

**Fix:** Review `knowledge_artifact_comparison_matrix.md` before creating

---

### Mistake 2: Not Testing Before Deployment

**Problem:** Create artifact, commit to team repo, breaks everyone's workflow

**Lesson:** Always test in isolation first
1. Create in personal directory
2. Test thoroughly
3. Get peer review
4. Deploy to team

---

### Mistake 3: No Examples in Documentation

**Problem:**
```yaml
description: Review code for quality
```

**User confusion:** How do I use this? What does it do exactly?

**Fix:** Always provide 3-5 concrete examples
```markdown
## Examples

### Example 1: Review Changed Files
User: "Review my changes"
→ Skill analyzes git diff, provides structured feedback

### Example 2: Security Audit
User: "Check auth.js for security issues"
→ Skill focuses on security vulnerabilities

[3 more examples...]
```

---

### Mistake 4: Placeholder Content

**Problem:**
```markdown
# TODO: Add methodology here
[FILL IN LATER]
```

**Result:** Artifact doesn't work, confuses users

**Prevention:** Complete all content before deployment, no TODOs

---

### Mistake 5: Not Restarting Claude After Changes

**Problem:** Made changes to Skill, but changes don't take effect

**Cause:** Claude loads artifacts at session start

**Fix:** Restart Claude Code session after making changes

---

## YAML Syntax Mistakes

### Mistake 1: Missing Closing ---

**Wrong:**
```yaml
---
name: my-skill
description: Does something
```

**Right:**
```yaml
---
name: my-skill
description: Does something
---

[Markdown content]
```

---

### Mistake 2: Tab Characters

**Wrong:**
```yaml
---
name: my-skill
	description: Something  # Tab before description
---
```

**Right:**
```yaml
---
name: my-skill
description: Something
---
```

**Prevention:** Use spaces, never tabs in YAML

---

### Mistake 3: Smart Quotes

**Wrong:**
```yaml
description: "Process files"  # Smart quotes: " "
```

**Right:**
```yaml
description: "Process files"  # Straight quotes: " "
```

**Prevention:** Use plain text editor, not Word

---

## Troubleshooting Checklist

**When artifact doesn't work:**

1. [ ] **Check file exists at correct location**
   - Skills: `.claude/skills/name/SKILL.md`
   - Commands: `.claude/commands/name.md`
   - Subagents: `.claude/agents/name.md`
   - Hooks: `.claude/settings.json`

2. [ ] **Validate YAML syntax**
   ```bash
   python3 -c "import yaml; yaml.safe_load(open('file.md').read().split('---')[1])"
   ```

3. [ ] **Check name matches directory/file**
   ```bash
   # For Skills
   basename $(pwd) == $(grep "^name:" SKILL.md | cut -d: -f2 | xargs)
   ```

4. [ ] **Restart Claude Code session**
   - Artifacts loaded at session start
   - Changes require restart

5. [ ] **Check Claude Code version**
   - Some features require recent versions
   - Update if needed

6. [ ] **Enable debug mode**
   ```bash
   claude --debug
   ```

7. [ ] **Review logs for errors**
   - YAML parsing errors
   - File not found errors
   - Permission issues

---

## Prevention Best Practices

### Before Creating Any Artifact

1. **Choose correct artifact type** - Consult comparison matrix
2. **Read specification** - Understand requirements fully
3. **Review common mistakes** - Don't repeat known errors
4. **Plan before coding** - Think through the approach

### During Creation

1. **Follow workflows** - Use step-by-step creation workflows
2. **Validate incrementally** - Check YAML syntax, test as you go
3. **Add examples** - Include 3-5 concrete examples
4. **Document thoroughly** - Clear descriptions and usage notes

### Before Deployment

1. **Test in isolation** - Never test in production first
2. **Get peer review** - Especially for Hooks (security critical)
3. **Validate fully** - Run complete validation checklist
4. **Document for team** - Add to README, notify team

### After Deployment

1. **Monitor** - Watch for errors, collect feedback
2. **Iterate** - Improve based on real usage
3. **Document learnings** - Add new mistakes to this context
4. **Share knowledge** - Help team avoid same mistakes

---

## When to Ask for Help

**Ask for help if:**
- Artifact not working after 3 troubleshooting attempts
- Security implications unclear (especially Hooks)
- Unsure which artifact type to use
- Hit edge case not covered in documentation
- Need peer review for security-critical Hook

**Where to get help:**
- Team members with Claude Code experience
- Claude Code documentation: docs.claude.com
- GitHub issues: github.com/anthropics/claude-code/issues

---

## Contributing to This Context

**This is a living document.** Add new mistakes as they're discovered:

**Template:**
```markdown
### Mistake N: [Brief Title]

**Problem:**
[What went wrong]

**Why it fails:**
[Explanation]

**Fix:**
[Solution]

**Prevention:**
[How to avoid]

**Discovered:** [Date, who found it]
```

---

## Related Documentation

- **Artifact specifications:** `knowledge_*_specification.md`
- **Creation workflows:** `instructions_*_creation_workflow.md`
- **Validation procedures:** `instructions_validation_and_testing.md`
- **Artifact comparison:** `knowledge_artifact_comparison_matrix.md`

---

## Document Metadata

**Quality Estimate:**
- Relevance (40%): 0.90 - Directly addresses common issues
- Completeness (30%): 0.86 - Covers major mistake categories
- Consistency (20%): 0.84 - Consistent format across examples
- Efficiency (10%): 0.82 - Dense with practical examples

**Overall Quality: B+ (0.86)** - Matches planned estimate

**Maintenance:**
- Add new mistakes as discovered
- Update based on team feedback
- Review quarterly for relevance
- Remove outdated patterns

**Last updated:** 2025-11-05
**Contributors:** [Team members who add mistakes]
