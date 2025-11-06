# Artifact Advisor: Decision Examples

This document provides concrete examples of artifact type recommendations for different use cases.

---

## Example 1: PDF Data Extraction

### User Request
"I work with a lot of PDF invoices and need to extract data from them regularly. Sometimes tables, sometimes specific fields. Can Claude help with this automatically?"

### Analysis

**Requirements gathered:**
- Purpose: Extract data from PDF files (tables, fields)
- Trigger: Automatic (user mentions PDFs)
- Reasoning: Yes (parsing, extraction, structuring)
- Complexity: Medium (different PDF formats)
- Frequency: Regular use

**Decision factors:**
- ✅ User naturally mentions "PDF" - good trigger
- ✅ Requires methodology (multiple extraction approaches)
- ✅ Benefits from examples (different PDF types)
- ✅ Should detect automatically

### Recommendation: **SKILL**

**Justification:**
```markdown
## Recommendation: Skill

### Why a Skill?
Your use case is perfect for a Skill because:
- Claude should automatically activate when you mention PDFs
- Extracting data requires specialized methodology (different tools for different PDF types)
- You'll benefit from having examples of various extraction patterns
- Progressive disclosure - complex PDF handling only loaded when needed

### Key Benefits:
- Automatic activation: Just say "extract data from this invoice.pdf"
- Domain expertise: Skill knows when to use pdfplumber vs PyPDF2 vs tabula
- Methodology: Step-by-step extraction workflows
- Examples: Handles invoices, reports, forms differently

### Example Activation:
User: "Can you extract the invoice items from quarterly-report.pdf?"
→ pdf-processor Skill activates automatically

### What to Create:
A Skill with description like:
"Extract text, images, and tables from PDFs. Parse invoices, reports, and forms. Handle multiple PDF formats using pdfplumber, PyPDF2, and tabula. Use when working with PDF files or document extraction tasks."

### Next Steps:
1. Use skill-builder Skill to create the PDF processor Skill
2. Include examples for invoices, reports, forms
3. Test activation with: "Process this PDF file"
```

**Anti-pattern avoided:**
❌ Command - Would require user to type `/extract-pdf` every time
✅ Skill - Automatic, natural interaction

---

## Example 2: Team Deployment Workflow

### User Request
"Our team needs to deploy to staging and production. I want a standardized process that checks tests, builds, and deploys. Team members should explicitly trigger this."

### Analysis

**Requirements gathered:**
- Purpose: Standardized deployment workflow
- Trigger: Manual/explicit (high-stakes operation)
- Reasoning: Minimal (execution of predefined steps)
- Complexity: Medium (multi-step with validation)
- Team-shared: Yes

**Decision factors:**
- ✅ Explicit trigger required (deployment is deliberate)
- ✅ High-stakes operation (shouldn't be automatic)
- ✅ Standardizes team process
- ✅ Benefits from arguments (environment, branch)

### Recommendation: **COMMAND**

**Justification:**
```markdown
## Recommendation: Command

### Why a Command?
Your deployment workflow is perfect for a Command because:
- Deployment should be **explicit**, not automatic (too risky)
- User consciously decides when to deploy
- Standardizes process across team
- Arguments make it flexible (staging vs production, branch selection)

### Key Benefits:
- Explicit invocation: User types `/deploy staging` when ready
- Arguments: `/deploy production main` specifies environment and branch
- Safety: Can use disable-model-invocation to prevent accidental Claude-triggered deployments
- Team consistency: Everyone follows same deployment process

### Example Usage:
```
/deploy staging feature-branch    # Deploy feature branch to staging
/deploy production main           # Deploy main to production
```

### What to Create:
A Command with:
```yaml
---
description: Deploy to specified environment with validation
argument-hint: <environment> [branch]
allowed-tools: Bash
disable-model-invocation: true  # Prevent accidental deployment
---

Deploy to $1 environment from ${2:-main} branch

[Pre-deployment checks]
[Build process]
[Deployment steps]
```

### Next Steps:
1. Use command-builder Skill to create deployment command
2. Add validation (tests must pass)
3. Document for team in README
```

**Anti-pattern avoided:**
❌ Skill - Deployment should never be automatic
✅ Command - Explicit, deliberate invocation

---

## Example 3: Comprehensive Code Review

### User Request
"I want thorough code reviews for pull requests - security, quality, best practices, the whole nine yards. This takes a while and I don't want it clogging up the main conversation."

### Analysis

**Requirements gathered:**
- Purpose: Detailed PR code reviews
- Trigger: Automatic delegation when review needed
- Reasoning: Yes (analysis, recommendations, security checks)
- Complexity: High (multiple review dimensions)
- Context isolation: Needed (reviews are lengthy)

**Decision factors:**
- ✅ Complex analysis benefits from isolation
- ✅ Main agent delegates when appropriate
- ✅ Specialized system prompt for review methodology
- ✅ Results returned to main conversation

### Recommendation: **SUBAGENT**

**Justification:**
```markdown
## Recommendation: Subagent

### Why a Subagent?
Your code review needs are perfect for a Subagent because:
- Reviews are **complex and focused** - benefit from isolated context
- Main agent can delegate when review needed
- Specialized system prompt defines review methodology
- Keeps main conversation clean (review details in subagent context)

### Key Benefits:
- Isolated context: Subagent has clean workspace for review
- Specialized focus: Dedicated to quality, security, best practices
- Autonomous: Completes full review independently
- Results summary: Returns findings to main agent

### Example Flow:
```
User: "Review the changes in PR #456"
Main agent: Recognizes review task
Main agent: Delegates to code-reviewer subagent
Subagent: Fetches PR, analyzes changes, generates report
Subagent: Returns structured review to main agent
Main agent: Presents summary to user
```

### What to Create:
A Subagent with:
```yaml
---
name: code-reviewer
description: Reviews code for quality, security, and best practices. Use for pull request reviews and code audits.
tools: Read, Grep, Glob, Bash
model: sonnet
---

# Code Review Specialist

You are a code reviewer focused on:
1. Security vulnerabilities
2. Code quality and maintainability
3. Best practices compliance

[Detailed review methodology]
[Output format specification]
```

### Next Steps:
1. Use subagent-builder Skill to create code reviewer
2. Define comprehensive review methodology
3. Specify structured output format
4. Test delegation with real PRs
```

**Anti-pattern avoided:**
❌ Skill - Would pollute main context with lengthy review details
✅ Subagent - Isolated, focused, returns clean summary

---

## Example 4: Automatic Linting Before Edits

### User Request
"I keep forgetting to run ESLint before committing. Can I make it run automatically every time I edit a JavaScript file?"

### Analysis

**Requirements gathered:**
- Purpose: Run ESLint before file edits
- Trigger: Event-driven (PreToolUse on Edit)
- Reasoning: No (simple validation)
- Complexity: Simple (run command, check exit code)
- Enforcement: Yes (block if fails)

**Decision factors:**
- ✅ Event-driven automation
- ✅ Simple validation (no reasoning)
- ✅ Can block operation if fails
- ✅ Enforces policy automatically

### Recommendation: **HOOK**

**Justification:**
```markdown
## Recommendation: Hook

### Why a Hook?
Your linting enforcement is perfect for a Hook because:
- Runs **automatically** on Edit tool use
- Simple validation - no reasoning needed
- Can block edits if linting fails
- Prevents unlinted code from being committed

### Key Benefits:
- Automatic: No need to remember to lint
- Preventive: Blocks bad edits before they happen
- Event-driven: Triggers on PreToolUse event
- Zero user effort: Just works

### Example Flow:
```
User: "Edit this JavaScript file"
Claude: Prepares to use Edit tool
Hook: Intercepts PreToolUse event
Hook: Runs eslint on file
Hook: If fails, blocks edit with error message
Hook: If passes, allows edit to proceed
```

### What to Create:
A Hook configuration:
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit.*\\.js$",
        "hooks": [
          {
            "type": "command",
            "command": "eslint \"$FILE_PATH\" --max-warnings 0",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### ⚠️ IMPORTANT: Security Review Required
Hooks execute arbitrary commands automatically - MANDATORY peer review before deployment!

### Next Steps:
1. Use hook-security-reviewer Skill to ensure safety
2. Create Hook configuration in settings.json
3. Test in isolated environment first
4. Get peer security review
5. Deploy to team
```

**Anti-pattern avoided:**
❌ Command - Requires user to remember `/lint` every time
✅ Hook - Automatic enforcement

---

## Example 5: API Testing Suite (Unclear Requirements)

### User Request
"I'm building a REST API and need to test endpoints frequently. Should this be automatic or manual? I'm not sure."

### Analysis

**Requirements gathered:**
- Purpose: Test REST API endpoints
- Trigger: **UNCLEAR** (need to ask)
- Reasoning: Yes (validation, response checking)
- Complexity: Medium (multiple test types)

**Decision factors:**
- ❓ Trigger model unclear - need clarification

### Recommendation: **ASK CLARIFYING QUESTIONS**

**Response:**
```markdown
## Need More Information

API testing could work as multiple artifact types depending on your workflow. Let me ask:

### Clarifying Questions:

**1. How do you want to trigger testing?**
   A) Automatically when I mention APIs or endpoints
   B) Explicitly with a command like `/test-api`
   C) Automatically after code changes (event-driven)

**2. What's the scope of testing?**
   A) Quick smoke tests (few seconds)
   B) Comprehensive test suite (minutes)
   C) Varies by situation

**3. Context needs?**
   A) Testing integrated with current conversation
   B) Testing isolated (returns summary only)

### Possible Recommendations Based on Answers:

**If A+A+A:** Skill
- Automatic activation when you mention APIs
- Quick testing integrated into conversation
- Examples: "Test the /users endpoint"

**If B+A+A:** Command
- Explicit invocation: `/test-api /users`
- User controls when to test
- Examples: `/test-api POST /users payload.json`

**If C+B+B:** Hook
- Automatic testing after API code changes
- Event-driven on PostToolUse
- Blocks if tests fail

**If A+B+B:** Subagent
- Automatic delegation for comprehensive testing
- Isolated context for lengthy test runs
- Returns summary of results

Please answer the questions so I can give you the best recommendation!
```

**Key lesson:**
When requirements are unclear, ASK before recommending. Better to clarify than guess wrong.

---

## Example 6: Multi-Artifact Solution (Git Workflows)

### User Request
"I want to help with git workflows - creating branches, committing with good messages, creating PRs."

### Analysis

**Requirements gathered:**
- Purpose: Multiple git workflow operations
- Trigger: Varies by operation
- Reasoning: Varies (commit messages need help, branch creation is execution)
- Complexity: Varies

**Decision factors:**
- 🔀 Multiple operations with different characteristics

### Recommendation: **COMBINATION OF ARTIFACTS**

**Response:**
```markdown
## Recommendation: Multiple Artifacts

Your git workflow needs are best served by a **combination of artifact types**:

### 1. Commit Message Helper - SKILL
**Why:** Automatic help when creating commits
```yaml
description: Help craft git commit messages following conventional commits format. Suggest message structure, validate format, provide examples. Use when creating commits or writing commit messages.
```
**Usage:** "Help me write a commit message for these changes"

### 2. Branch Creation - COMMAND
**Why:** Explicit workflow initiation
```yaml
description: Create feature branch following team naming conventions
argument-hint: <feature-name>
```
**Usage:** `/feature-start user-authentication`

### 3. PR Creation - COMMAND
**Why:** Deliberate PR creation with structured info
```yaml
description: Create pull request with description and checklist
argument-hint: <pr-title>
```
**Usage:** `/create-pr "Add user authentication"`

### 4. Pre-Commit Validation - HOOK
**Why:** Automatic enforcement before commits
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash.*git commit",
        "hooks": [{"command": "npm test"}]
      }
    ]
  }
}
```

### Why Multiple Artifacts?
- Different operations have different trigger needs
- Some require reasoning (commit messages) - Skill
- Some are explicit workflows (branch creation) - Command
- Some are enforcement (pre-commit checks) - Hook
- Each artifact type optimized for its specific operation

### Implementation Priority:
1. **Start with:** Commit message Skill (most frequent)
2. **Then add:** Branch/PR Commands (standardize workflows)
3. **Finally:** Pre-commit Hook (enforcement)

This modular approach gives you flexibility and appropriate automation for each operation.
```

**Key lesson:**
Don't force everything into one artifact type. Use the right tool for each job.

---

## Example 7: When No Artifact Needed

### User Request
"I want Claude to read and summarize configuration files."

### Analysis

**Requirements gathered:**
- Purpose: Read and summarize configs
- Trigger: As needed in conversation
- Reasoning: Yes (summarization)
- Complexity: Low (main agent can handle)

**Decision factors:**
- ✅ Main agent already has Read tool
- ✅ Summarization is standard capability
- ❌ No special methodology needed
- ❌ No repeated pattern requiring shortcut

### Recommendation: **NO ARTIFACT NEEDED**

**Response:**
```markdown
## Recommendation: No Artifact Needed

For reading and summarizing configuration files, you **don't need an artifact** - Claude can do this directly in the conversation using the Read tool.

### Why No Artifact?
- Claude already has Read tool access
- Summarization is a standard capability
- No specialized methodology required
- No need for shortcuts or automation

### Just Ask Directly:
```
"Read config.json and summarize the key settings"
"What's configured in .env.example?"
"Summarize the database configuration"
```

### When You WOULD Need an Artifact:

**Skill** - If you need specialized config analysis methodology:
- "Validate configs against best practices"
- "Compare configs across environments"
- "Analyze config security"

**Command** - If you want a shortcut for repeated operations:
- `/check-config` - standardized config review
- `/compare-configs dev prod` - environment comparison

**Subagent** - If config analysis is complex and lengthy:
- Comprehensive config audit
- Security analysis of all configs
- Performance impact analysis

### Current Recommendation:
Start without an artifact. If you find yourself repeatedly asking for specific config analysis patterns, then consider creating a Skill or Command.
```

**Key lesson:**
Don't over-engineer. Use artifacts when they add value, not just because you can.

---

## Summary of Decision Patterns

| User Need | Artifact Type | Key Indicator |
|-----------|---------------|---------------|
| Automatic domain help | Skill | "Claude should detect when I need this" |
| Explicit workflow | Command | "User should deliberately trigger" |
| Complex sub-task | Subagent | "Needs isolation and specialized focus" |
| Event automation | Hook | "Should run automatically on events" |
| Multiple operations | Combination | Different needs per operation |
| Simple capability | None | Main agent already handles it |

---

**These examples demonstrate the decision-making process for various use cases. Use them as reference when consulting with users.**
