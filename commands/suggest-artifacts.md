---
description: Analyze workflows and suggest Claude Code artifacts to build. Identifies pain points, repetitive tasks, and automation opportunities. Recommends specific Skills, Commands, Subagents, or Hooks based on user's work patterns and technology usage.
argument-hint: [workflow-domain]
allowed-tools: Read, Grep, Glob
---

# Workflow Analysis & Artifact Suggestions

Help users identify WHAT Claude Code artifacts to build based on their workflows, pain points, and work patterns.

## Objective

Analyze the user's development workflow to:
1. Identify repetitive tasks and pain points
2. Recognize technology patterns (PDFs, APIs, testing, git, etc.)
3. Suggest 2-4 specific artifacts that would provide the most value
4. Prioritize suggestions by impact and ease of implementation
5. Provide clear next steps for creation

## Usage

**With domain focus:**
```bash
/suggest-artifacts testing
/suggest-artifacts pdf-processing
/suggest-artifacts git
```

**General workflow analysis:**
```bash
/suggest-artifacts
```

## Analysis Methodology

### Step 0: Documentation Verification (2-3 min)

**Before analyzing workflows, verify current artifact specifications:**

Use the `claude-researcher` skill to check the latest official documentation:

**Query:** "What are the current specifications and capabilities for Skills, Commands, Subagents, and Hooks from the official Claude Code documentation? Include when to use each artifact type."

**Verify:**
- Current artifact type definitions and purposes
- When to use each artifact type (decision framework)
- Available features and capabilities for each type
- Recent additions or changes to artifact types
- Best practices for artifact selection

**Alignment check:** Ensure suggestions will be based on the most current artifact capabilities and patterns from code.claude.com.

If specifications have changed significantly, adjust the suggestion patterns accordingly.

---

### Step 1: Workflow Discovery

If the user provided a domain argument (e.g., `/suggest-artifacts testing`):
- Focus analysis on that specific domain
- Ask targeted questions about their workflow in that area
- Identify specific pain points and opportunities

If no argument provided:
- Ask about their daily development tasks
- Identify file types and technologies they work with
- Discover repetitive manual processes
- Spot collaboration and team workflow needs

**Discovery Questions:**
- What tasks do you do repeatedly?
- What file types or technologies do you work with most?
- What manual processes slow you down?
- What do you wish was automated?
- What tools or workflows does your team use?

### Step 2: Pattern Recognition

Map workflow descriptions to artifact patterns:

**Technology Patterns:**
- **PDFs** → PDF processing Skill (extract, merge, convert)
- **APIs** → API testing Skill or testing Command
- **Databases** → SQL optimization Skill or query Command
- **Testing** → Test runner Command or pre-commit Hook
- **Git** → Git workflow Commands or commit Hooks
- **Deployment** → Deployment Commands (explicit control)
- **Code Review** → Review Subagent (isolated analysis)
- **Logs** → Log analysis Skill (pattern detection)
- **Data** → Data analysis Subagent (complex processing)
- **Documentation** → Doc generation Command

**Pain Point Patterns:**
- **"I forget to..."** → Hook (automatic enforcement)
- **"I do this every day..."** → Command (standardized workflow)
- **"I need help with..."** → Skill (automatic guidance)
- **"This takes too long..."** → Subagent (delegated processing)
- **"I need to analyze..."** → Skill or Subagent (reasoning required)
- **"Team needs consistency..."** → Command (shared process)

### Step 3: Artifact Suggestion

For each identified opportunity, suggest a specific artifact with:

1. **Artifact Name & Type** (e.g., "PDF Processor Skill")
2. **Impact Level** (HIGH/MEDIUM/LOW)
3. **What It Does** (clear capability description)
4. **Example Usage** (how it would be used)
5. **Benefits** (time saved, consistency, etc.)

**Prioritization Criteria:**
- **HIGH Impact:** Addresses frequent, time-consuming tasks
- **MEDIUM Impact:** Improves occasional workflows or team consistency
- **LOW Impact:** Nice-to-have conveniences

### Step 4: Recommendation Format

Present suggestions in this format:

```markdown
Based on your workflow, here are artifacts that could help:

## 1. [Artifact Name] (HIGH IMPACT)
**Type:** Skill/Command/Subagent/Hook
**What it does:** [Clear description]
**Example usage:** [Concrete example]
**Benefits:** [Value proposition]

## 2. [Artifact Name] (MEDIUM IMPACT)
[Same structure]

## 3. [Artifact Name] (LOW IMPACT)
[Same structure]

---

**Recommendation:** Start with #1 as it will provide the most immediate value.

Would you like help creating [#1]?
```

### Step 5: Handoff to Creation

After presenting suggestions:
- If user wants to proceed → hand off to artifact-advisor (confirms type)
- If user wants more options → iterate on suggestions
- If user wants examples → provide detailed use cases
- If unclear on choice → help user decide between options

## Common Workflow Scenarios

### Scenario 1: PDF Work

**User context:** "I work with PDF invoices and reports"

**Suggestions:**
1. **PDF Processor Skill** (HIGH) - Automatic extraction and conversion
2. **PDF Form Filler Command** (MEDIUM) - Batch form processing
3. **PDF Validation Hook** (LOW) - Pre-commit PDF checks

### Scenario 2: API Development

**User context:** "I'm building and testing REST APIs"

**Suggestions:**
1. **API Testing Skill** (HIGH) - Automatic endpoint testing
2. **OpenAPI Validator Command** (MEDIUM) - Spec validation
3. **API Doc Generator Command** (MEDIUM) - Auto-generate documentation

### Scenario 3: Testing Workflows

**User context:** "I need better testing automation"

**Suggestions:**
1. **Pre-Commit Test Hook** (HIGH) - Automatic test runs before commits
2. **Test Runner Command** (MEDIUM) - Flexible test execution
3. **Test Generator Subagent** (MEDIUM) - Comprehensive test suite creation

### Scenario 4: Git Workflows

**User context:** "I want to improve our git process"

**Suggestions:**
1. **Commit Message Helper Skill** (HIGH) - Automatic commit message guidance
2. **Branch Creation Command** (MEDIUM) - Standardized branching
3. **PR Creation Command** (MEDIUM) - Consistent PR structure
4. **Pre-Commit Lint Hook** (MEDIUM) - Enforce code quality

### Scenario 5: Code Review

**User context:** "I need thorough code reviews"

**Suggestions:**
1. **Code Review Subagent** (HIGH) - Detailed isolated analysis
2. **PR Review Command** (MEDIUM) - Quick review workflow
3. **Security Scanner Hook** (HIGH) - Automatic vulnerability checks

### Scenario 6: Deployment

**User context:** "I deploy to multiple environments"

**Suggestions:**
1. **Deployment Command** (HIGH) - Explicit, safe deployment workflow
2. **Pre-Deploy Validation Hook** (MEDIUM) - Verify before deployment
3. **Environment Config Skill** (LOW) - Help manage configs

### Scenario 7: Database Work

**User context:** "I write SQL queries frequently"

**Suggestions:**
1. **SQL Optimization Skill** (HIGH) - Query analysis and improvement
2. **Migration Generator Command** (MEDIUM) - Create migration files
3. **Schema Validator Command** (MEDIUM) - Check schema consistency

### Scenario 8: Log Analysis

**User context:** "I debug issues by analyzing logs"

**Suggestions:**
1. **Log Analyzer Skill** (HIGH) - Pattern detection and error finding
2. **Log Filter Command** (MEDIUM) - Extract relevant entries
3. **Alert Generator Subagent** (LOW) - Analyze and create alerts

## Technology-to-Artifact Quick Reference

| Technology/Task | Primary Suggestion | Secondary Options |
|----------------|-------------------|-------------------|
| PDFs | PDF Processor Skill | Form Filler Command, Validation Hook |
| APIs | API Testing Skill | Test Command, Doc Generator |
| Testing | Pre-Commit Hook | Test Runner Command, Test Generator Subagent |
| Git | Commit Helper Skill | Branch/PR Commands, Lint Hook |
| Code Review | Review Subagent | Review Command, Security Hook |
| Deployment | Deployment Command | Validation Hook, Config Skill |
| SQL/Databases | SQL Optimization Skill | Migration Command, Schema Validator |
| Logs | Log Analyzer Skill | Filter Command, Alert Subagent |
| Data Analysis | Analysis Subagent | Visualization Command, Stats Skill |
| Documentation | Doc Generator Command | Doc Validator Hook, Style Skill |

## Decision Framework Integration

After suggesting artifacts, if the user wants to proceed:

1. **Confirm artifact type** with artifact-advisor Skill
2. **Create the artifact** with appropriate builder (skill-builder, etc.)
3. **Validate quality** with artifact-validator Skill

Example flow:
```
User: /suggest-artifacts
→ suggest-artifacts analyzes and suggests "PDF Processor Skill"
→ User: "Let's build it"
→ artifact-advisor confirms "Skill is correct type because..."
→ skill-builder guides creation step-by-step
→ artifact-validator checks quality
```

## Edge Cases

### When No Clear Artifact Needed

If the user's workflow is already well-served:
```markdown
Based on your workflow, Claude's built-in capabilities may already cover your needs:
- [Capability 1] - Use Read tool directly
- [Capability 2] - Use Bash tool for this

However, you might still benefit from:
- [Optional artifact suggestion with justification]

Is there a specific pain point you'd like to address?
```

### When Multiple Artifacts Needed

Some workflows benefit from multiple complementary artifacts:
```markdown
Your workflow would benefit from a combination:

1. [Artifact 1] - For [specific need]
2. [Artifact 2] - For [different need]
3. [Artifact 3] - For [another need]

These work together: [explain how they complement each other]

Recommended implementation order:
1. Start with [Artifact 1] (highest impact)
2. Add [Artifact 2] once #1 is working
3. Consider [Artifact 3] for polish
```

### When Domain is Ambiguous

If the provided domain argument is unclear:
```markdown
I'm not sure what you mean by "[domain]". Could you clarify:

- Are you referring to [interpretation 1]?
- Or do you mean [interpretation 2]?
- Or something else: [alternative]?

This will help me suggest the most relevant artifacts.
```

## Best Practices

1. **Listen First** - Understand the workflow before suggesting
2. **Be Specific** - Suggest concrete artifacts with clear names
3. **Prioritize by Impact** - Focus on high-value opportunities
4. **Provide Examples** - Show how artifacts would be used
5. **Limit Suggestions** - 2-4 artifacts max to avoid overwhelm
6. **Explain Benefits** - Make value proposition clear
7. **Facilitate Handoff** - Smoothly transition to creation process
8. **Consider Alternatives** - Explain why some approaches don't fit
9. **Validate Understanding** - Confirm workflow understanding
10. **Encourage Iteration** - Users can come back for more suggestions

## Anti-Patterns to Avoid

❌ **Don't suggest artifacts for every minor task**
- Main conversation can handle simple requests
- Only suggest when there's clear repeated value

❌ **Don't overwhelm with options**
- Limit to 2-4 suggestions, prioritized
- Focus on highest impact opportunities

❌ **Don't assume without asking**
- Always validate understanding of workflow
- Ask clarifying questions when needed

❌ **Don't suggest wrong artifact types**
- Use decision framework to recommend correct type
- Explain why each type fits (or doesn't)

❌ **Don't end without action**
- Always provide clear next steps
- Offer to help with creation

## Success Criteria

A successful consultation results in:
- ✅ User understands what artifacts would help their workflow
- ✅ User has 2-4 prioritized, concrete suggestions
- ✅ User knows the benefits and example usage
- ✅ User has clear next steps to create artifacts
- ✅ User feels confident in the recommendations

---

**Integration with Ouroboros Plugin:**
This command is part of the Ouroboros plugin and works alongside:
- **artifact-advisor** - Confirms artifact type choice
- **skill-builder** - Guides Skill creation
- **artifact-validator** - Validates quality
- **claude-expert** - Provides specifications
- **claude-researcher** - Fetches official docs
