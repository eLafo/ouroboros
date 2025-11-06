# Artifact Comparison Matrix: Skills vs Commands vs Subagents vs Hooks

## Document Purpose

This context provides a **comprehensive comparison framework** for deciding which Claude Code artifact type to use: Skills, Slash Commands, Subagents, or Hooks. Use this as the foundational decision-making reference when creating new Claude Code extensions.

**Target audience:** Developers creating Claude Code plugins or project-specific workflows
**Usage:** Consult BEFORE creating any artifact to ensure correct type selection

---

## Quick Decision Tree

```
START: What do you want to achieve?

┌─ I want Claude to AUTOMATICALLY do something based on context
│  ├─ It's a specialized workflow requiring extended context
│  │  └─→ USE SKILL (proactive, model-invoked, custom context)
│  │
│  ├─ It's a focused sub-task with its own context window
│  │  └─→ USE SUBAGENT (delegated task, isolated context)
│  │
│  └─ It's a reaction to specific events (file changes, tool use, etc.)
│     └─→ USE HOOK (event-driven, automated validation/enforcement)
│
└─ I want the USER to explicitly trigger something
   └─→ USE SLASH COMMAND (manual invocation, no activation logic)
```

---

## Comprehensive Comparison Table

| Dimension | **Skill** | **Slash Command** | **Subagent** | **Hook** |
|-----------|-----------|-------------------|--------------|----------|
| **Invocation** | Automatic (model-invoked) | Explicit user command (`/name`) | Automatic delegation by main agent | Automatic (event-triggered) |
| **Activation Logic** | Claude reads description and decides | User types command | Main agent delegates via Task tool | Event matcher + trigger condition |
| **File Format** | Directory with `SKILL.md` + YAML frontmatter | Single `.md` file (optional frontmatter) | `.md` file with YAML frontmatter | JSON in `settings.json` |
| **Storage Location** | `.claude/skills/skill-name/` | `.claude/commands/file.md` | `.claude/agents/agent-name.md` | `.claude/settings.json` or `settings.local.json` |
| **Context Management** | Separate context loaded when activated | Runs in main conversation context | Isolated context window (separate from main) | No context (executes command/prompt) |
| **Tool Access** | Can restrict via `allowed-tools` | Can restrict via `allowed-tools` | Can restrict via `tools` field | Executes bash or LLM prompt |
| **Model Selection** | Inherits main conversation model | Can specify via `model` field | Can specify via `model` field (sonnet/opus/haiku) | Prompt hooks can use LLM |
| **Arguments** | N/A (activation is contextual) | `$ARGUMENTS`, `$1`, `$2`, etc. | Passed via Task tool `prompt` parameter | `$ARGUMENTS` in hook command |
| **Use Case** | Specialized workflows (PDF processing, API testing) | Repetitive commands, team workflows | Complex sub-tasks (code review, debugging) | Validation, policy enforcement, CI integration |
| **Best For** | Domain-specific capabilities requiring extended guidance | Simple, repeatable actions with explicit invocation | Delegating focused work with dedicated context | Blocking unsafe operations, enforcing standards |
| **Discoverability** | Claude uses based on description | `/help` lists all commands | Invisible to user (behind the scenes) | Invisible to user (automated) |
| **Security Model** | Tool restriction only | Tool restriction + allowed bash commands | Tool restriction | **HIGH RISK**: arbitrary shell execution |
| **Complexity** | Medium (description crafting critical) | Low (straightforward prompt expansion) | Medium-High (system prompt design) | High (security review mandatory) |
| **Team Sharing** | Commit to `.claude/skills/` | Commit to `.claude/commands/` | Commit to `.claude/agents/` | Commit to `.claude/settings.json` (caution!) |

---

## Detailed Artifact Characteristics

### 1. Skills

**Primary Purpose:** Enable Claude to autonomously perform specialized workflows when contextually appropriate.

**Activation Mechanism:**
- Claude reads the `description` field in YAML frontmatter
- Decides to activate the Skill based on user request + description match
- The Skill context (entire `SKILL.md` + supporting files) gets loaded

**When to Use:**
- ✅ You want Claude to **automatically** perform domain-specific tasks
- ✅ The task requires extended guidance (methodology, examples, references)
- ✅ You want progressive disclosure (supporting docs loaded only when needed)
- ✅ The workflow is complex enough to need dedicated context

**When NOT to Use:**
- ❌ User should explicitly trigger the action → Use Command instead
- ❌ Task is a one-liner or simple prompt expansion → Use Command
- ❌ You need isolated sub-task execution → Use Subagent instead

**File Structure:**
```
.claude/skills/pdf-expert/
├── SKILL.md          # Main skill with YAML frontmatter
├── examples.md       # Supporting documentation
├── templates/        # Optional: templates, scripts, etc.
└── reference.md
```

**YAML Example:**
```yaml
---
name: pdf-expert
description: Extract text and tables from PDFs, fill PDF forms, merge documents. Use when working with PDF files or document processing tasks.
allowed-tools: Bash, Read, Write, Grep
---
```

**Key Success Factor:** The `description` field must contain **concrete trigger terms** that users naturally mention ("PDF", "document processing", "extract tables").

---

### 2. Slash Commands

**Primary Purpose:** Provide explicit, user-triggered shortcuts for repetitive tasks or team workflows.

**Activation Mechanism:**
- User types `/command-name arguments`
- Claude expands the command content (markdown) as if user typed it
- Executes in the main conversation context

**When to Use:**
- ✅ User should **explicitly** trigger the action
- ✅ The task is repetitive and benefits from shortcuts
- ✅ Team workflows need standardization (code reviews, deployments)
- ✅ Simple prompt expansion or command execution is sufficient

**When NOT to Use:**
- ❌ Claude should automatically detect when to use it → Use Skill instead
- ❌ Task needs isolated context or extended reasoning → Use Subagent
- ❌ Needs to run automatically on events → Use Hook

**File Structure:**
```
.claude/commands/
├── review-pr.md
├── deploy.md
└── test-suite.md
```

**Example with Arguments:**
```markdown
---
description: Review pull request with specified focus area
argument-hint: <pr-number> <focus-area>
allowed-tools: Bash
---

Review pull request #$1 focusing on $2 aspects.

Steps:
1. Fetch PR: `gh pr view $1`
2. Analyze changes with emphasis on $2
3. Provide structured feedback
```

**Invocation:** `/review-pr 456 security`
- `$1` = `456`
- `$2` = `security`

**Key Success Factor:** Clear `argument-hint` and `description` for discoverability via `/help`.

---

### 3. Subagents

**Primary Purpose:** Delegate focused sub-tasks to specialized agents with isolated context windows and custom system prompts.

**Activation Mechanism:**
- Main agent uses Task tool with `subagent_type` parameter
- Subagent operates in **separate context window** (doesn't consume main context)
- Returns final result to main agent

**When to Use:**
- ✅ Task is complex and benefits from **dedicated context window**
- ✅ Need specialized system prompt for focused behavior
- ✅ Task can be delegated independently (code review, debugging, research)
- ✅ Want to preserve main context for broader coordination

**When NOT to Use:**
- ❌ Task is simple and fits in main context → Use Skill or Command
- ❌ User should trigger explicitly → Use Command
- ❌ Need event-driven automation → Use Hook

**File Structure:**
```
.claude/agents/
├── code-reviewer.md
├── debugger.md
└── test-generator.md
```

**YAML Example:**
```yaml
---
name: code-reviewer
description: Reviews code for quality, security, and best practices. Use for pull request reviews and code audits.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You are a code review specialist. When reviewing code:

1. Run `git diff` to see changes
2. Check for security vulnerabilities (SQL injection, XSS, etc.)
3. Evaluate code quality and maintainability
4. Provide actionable feedback with line numbers

Focus on:
- Security issues (highest priority)
- Performance problems
- Code style violations
- Missing error handling
```

**Invocation (by main agent):**
```python
Task(
  subagent_type="code-reviewer",
  prompt="Review the changes in PR #456 focusing on security concerns",
  description="Code review for PR 456"
)
```

**Key Success Factor:** Clear `description` for automatic delegation + focused system prompt.

---

### 4. Hooks

**Primary Purpose:** Automatically execute validation, enforcement, or integration logic in response to specific Claude Code events.

**Activation Mechanism:**
- Event occurs (tool use, prompt submit, session start, etc.)
- Matcher pattern evaluates (regex on tool name or event type)
- Hook command executes (bash script or LLM prompt)
- Hook response can approve, block, or modify behavior

**When to Use:**
- ✅ Need to **validate** operations before execution (security checks)
- ✅ Enforce team policies automatically (no commits to main, lint before edit)
- ✅ Integrate with external systems (CI/CD, issue trackers)
- ✅ React to specific events (file changes, tool usage)

**When NOT to Use:**
- ❌ Need Claude to perform reasoning → Use Skill or Subagent
- ❌ User should trigger manually → Use Command
- ❌ Don't need event-driven automation → Consider other types

**Configuration Location:**
```
.claude/settings.json
.claude/settings.local.json  (gitignored, for personal hooks)
~/.claude/settings.json      (global)
```

**JSON Example:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "eslint \"$FILE_PATH\" --fix",
            "timeout": 30
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if user prompt contains sensitive data like API keys or passwords. Block if found.",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Available Event Types:**
- `PreToolUse` - Before tool execution (can block/modify)
- `PostToolUse` - After tool success
- `UserPromptSubmit` - When user submits input
- `SessionStart` - Session initialization
- `SessionEnd` - Session termination
- `Stop` - Main agent finishes
- `SubagentStop` - Subagent completes
- `Notification` - Permission requests or idle state

**Key Success Factor:** **MANDATORY security review** - hooks execute arbitrary commands automatically!

---

## Decision Framework: Choosing the Right Artifact

### Scenario 1: "I want Claude to help with PDF processing"

**Question:** Should the user explicitly trigger this, or should Claude automatically detect PDF-related tasks?

**Answer:** Automatically detect → **SKILL**

**Reasoning:**
- Users naturally say "extract data from this PDF" without thinking about commands
- Task benefits from extended guidance (PDF libraries, extraction techniques)
- Supporting documentation can be loaded progressively

**Implementation:**
```yaml
---
name: pdf-processor
description: Extract text, images, and tables from PDFs. Fill forms, merge documents, convert formats. Use when user mentions PDF files or document processing.
---
```

---

### Scenario 2: "I want a shortcut for running our test suite"

**Question:** Should Claude automatically decide when to run tests?

**Answer:** No, tests should be explicit → **SLASH COMMAND**

**Reasoning:**
- Running tests is a deliberate action with cost (time, resources)
- User wants control over when tests execute
- Simple command execution, no complex reasoning needed

**Implementation:**
```markdown
---
description: Run full test suite with coverage report
allowed-tools: Bash
---

Running test suite...

npm test -- --coverage
```

**Invocation:** `/test-suite`

---

### Scenario 3: "I want Claude to review code before committing"

**Question:** Is this a validation/enforcement task or a reasoning task?

**Sub-question:** Should it **block** commits or **assist** with reviews?

**Answer A (Blocking):** Block bad commits → **HOOK**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash.*git commit",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint && npm test",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

**Answer B (Assistance):** Help with reviews → **SUBAGENT**
```yaml
---
name: code-reviewer
description: Reviews code changes for quality and security
tools: Read, Grep, Bash
---
```

**Reasoning:**
- Hooks enforce (prevent bad actions)
- Subagents assist (provide analysis and recommendations)

---

### Scenario 4: "I want Claude to analyze large datasets"

**Question:** Does this need a separate context window?

**Answer:** Yes, analysis is extensive → **SUBAGENT**

**Reasoning:**
- Data analysis can consume significant context
- Isolating it in a subagent preserves main conversation for coordination
- Can use specialized model (e.g., Sonnet for analytical capability)

**Implementation:**
```yaml
---
name: data-analyst
description: Analyze datasets, generate reports, create visualizations. Use for data science and analytics tasks.
model: sonnet
tools: Bash, Read, Write, mcp__jupyter__execute
---
```

---

### Scenario 5: "I want to standardize git commit messages"

**Question:** Is this enforcement or assistance? Should it block or guide?

**Answer A (Enforcement):** Block bad commit messages → **HOOK**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash.*git commit",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if commit message follows conventional commits format (feat:, fix:, etc.). Block if invalid. Commit message: $ARGUMENTS",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

**Answer B (Assistance):** Guide user to good messages → **SKILL**
```yaml
---
name: commit-message-helper
description: Draft git commit messages following conventional commits format. Use when creating commits.
---
```

**Reasoning:**
- Enforcement = Hook (blocks non-compliant actions)
- Guidance = Skill (helps craft compliant messages)

---

## Anti-Patterns: Common Mistakes

### ❌ Anti-Pattern 1: Using Skills for Explicit User Actions

**Bad:**
```yaml
---
name: deploy-to-production
description: Deploy application to production server
---
```

**Why it's bad:** Deployment is a deliberate, high-stakes action. User should explicitly trigger it, not rely on Claude to "detect" deployment intent.

**Correct:** Use a Slash Command
```markdown
---
description: Deploy application to production
allowed-tools: Bash
---

⚠️ Deploying to production...

npm run deploy:prod
```

Invocation: `/deploy-prod`

---

### ❌ Anti-Pattern 2: Using Commands for Automatic Workflows

**Bad:**
```markdown
---
description: Check for security vulnerabilities in dependencies
---

Run: npm audit
```

**Why it's bad:** Security checks should happen automatically (when dependencies change), not require user to remember to run `/security-check`.

**Correct:** Use a Hook
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write.*package\\.json",
        "hooks": [
          {
            "type": "command",
            "command": "npm audit --audit-level=high",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

---

### ❌ Anti-Pattern 3: Using Hooks for Reasoning Tasks

**Bad:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "claude-review-code.sh \"$FILE_PATH\"",
            "timeout": 300
          }
        ]
      }
    ]
  }
}
```

**Why it's bad:** Code review requires reasoning, not just validation. Hooks are for quick checks, not extended analysis.

**Correct:** Use a Subagent (delegated when needed)
```yaml
---
name: code-reviewer
description: Review code for quality, security, and best practices
tools: Read, Grep, Bash
---
```

**Or** use a Skill (automatic activation)
```yaml
---
name: code-review-assistant
description: Review code changes for best practices and security issues. Use after significant code edits.
---
```

---

### ❌ Anti-Pattern 4: Using Subagents for Simple Tasks

**Bad:**
```yaml
---
name: file-reader
description: Read files from the filesystem
tools: Read
---

Read the requested file and return its contents.
```

**Why it's bad:** Reading files doesn't need a separate context window. This wastes resources and adds unnecessary complexity.

**Correct:** Let main agent use Read tool directly (no artifact needed)

**Or** if you want automatic file reading in certain contexts, use a Skill:
```yaml
---
name: config-analyzer
description: Analyze configuration files (package.json, tsconfig.json, etc.) for issues and recommendations. Use when user asks about project configuration.
allowed-tools: Read, Grep
---
```

---

## Summary: Quick Reference

| **Use Case** | **Artifact Type** | **Key Indicator** |
|-------------|-------------------|-------------------|
| Automatic domain workflow | Skill | "Claude should detect when to use this" |
| Explicit user command | Slash Command | "User should trigger this deliberately" |
| Complex delegated sub-task | Subagent | "Needs its own context window" |
| Event-driven validation | Hook | "Should run automatically on [event]" |
| Security enforcement | Hook | "Must block unsafe operations" |
| Team workflow standardization | Slash Command | "Everyone should use the same process" |
| Specialized assistant | Skill or Subagent | "Requires extended expertise" |
| Integration with external tool | Hook | "Connect to CI/CD, issue tracker, etc." |

---

## Next Steps After Choosing Artifact Type

Once you've determined the appropriate artifact type:

1. **For Skills:** Consult `knowledge_skill_specification.md` and follow `instructions_skill_creation_workflow.md`
2. **For Commands:** Consult `knowledge_command_specification.md` and follow `instructions_command_creation_workflow.md`
3. **For Subagents:** Consult `knowledge_subagent_specification.md` and follow `instructions_subagent_creation_workflow.md`
4. **For Hooks:** Consult `knowledge_hook_specification.md` and follow `instructions_hook_creation_workflow.md` (**mandatory security review**)

All artifacts should go through validation using `instructions_validation_and_testing.md`.

---

## Document Metadata

**Quality Estimate:**
- Relevance (40%): 0.95 - Directly addresses artifact selection decision
- Completeness (30%): 0.90 - Covers all four types with examples and anti-patterns
- Consistency (20%): 0.85 - Consistent terminology and framework
- Efficiency (10%): 0.80 - Dense information, some examples could be more concise

**Overall Quality: A (0.88)** - Matches planned estimate

**Maintenance:** Update when new artifact types are introduced or selection criteria change based on user feedback patterns.
