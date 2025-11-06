---
description: Guide creation of Claude Code Commands with step-by-step workflows, argument handling, frontmatter configuration, and testing. Ensures Commands follow best practices and correct YAML structure.
argument-hint: [command-name] [description]
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Command Builder

Guide users through creating Claude Code slash commands with emphasis on clarity, argument handling, and team usability.

## Objective

Help users create effective Claude Code slash commands that:
1. Provide clear, repeatable workflows
2. Handle arguments properly
3. Use appropriate tool restrictions
4. Follow best practices
5. Work reliably for the team

## Usage

**With description only (infer name):**
```bash
/build-command "Deploy application to specified environment"
```

**With command name only:**
```bash
/build-command deploy
```

**With both name and description:**
```bash
/build-command deploy "Deploy application to specified environment"
```

**General command creation:**
```bash
/build-command
```

## Arguments Handling

**Argument detection logic:**

1. **If $1 contains spaces or starts with uppercase** → Treat $1 as description, infer command name from it
2. **If $1 is lowercase/kebab-case AND $2 is provided** → $1 is name, $2 is description
3. **If only $1 is provided and looks like a name** (lowercase, no spaces, short) → $1 is command name, ask for description
4. **If no arguments** → Interactive mode, ask all questions

**Command name inference:**
When inferring from description, extract key action verbs and convert to kebab-case:
- "Deploy application to environments" → `deploy`
- "Review pull request for quality" → `review-pr`
- "Run tests and generate coverage" → `run-tests`
- "Analyze log files for errors" → `analyze-logs`

**Example invocations:**
- `/build-command` → Interactive mode
- `/build-command deploy` → Name: "deploy", ask for description
- `/build-command "Deploy to staging"` → Infer name: "deploy", description provided
- `/build-command deploy "Deploy to staging"` → Name: "deploy", description provided

## Command Creation Workflow

Follow this 7-step workflow to create a high-quality Command:

### Step 1: Planning (5-10 min)

**Gather requirements:**

**First, determine what was provided:**
- If $1 contains spaces or starts uppercase → It's a description (infer command name in Step 2)
- If $1 is kebab-case AND $2 exists → $1 is name, $2 is description
- If only $1 and it's kebab-case → $1 is name, need description
- If no arguments → Need both name and description

**If description was provided (either as $1 or $2):** Use it as the command's purpose and primary task. You may still need to ask clarifying questions, but the core purpose is established.

**If no description provided, ask the user:**

1. **What task will this Command perform?**
   Examples: Deploy application, review PR, run tests, analyze logs

2. **Who will use it?**
   - Just you (user-level: `~/.claude/commands/`)
   - Your team (project-level: `.claude/commands/`)

3. **What arguments are needed?**
   - None (simple prompt expansion)
   - One argument (e.g., environment name)
   - Multiple arguments (e.g., PR number + focus area)
   - Optional arguments (e.g., branch name with default)

4. **Should Claude have full tool access?**
   - Read-only (analysis, review)
   - Shell execution (testing, deployment)
   - File modification (editing, generation)
   - Web access (research, API calls)

5. **Is this destructive or requires human judgment?**
   - If yes, consider `disable-model-invocation: true`

**Output of Step 1:** Clear understanding of Command purpose, scope, and requirements

---

### Step 2: Command Naming (2 min)

**Create a clear, memorable command name:**

**Case 1: Command name explicitly provided ($1 is kebab-case)**
- Validate it follows the naming rules below
- If valid, use "$1" as the command name and proceed to Step 3

**Case 2: Description provided as $1 (contains spaces or uppercase)**
- Infer command name from the description using these patterns:
  - Extract primary action verb (deploy, review, run, analyze, etc.)
  - Add secondary noun if needed (deploy → deploy-app, review → review-pr)
  - Convert to kebab-case
- Show inferred name to user for confirmation
- If user disagrees, ask for preferred name

**Case 3: No command name provided**
Create one following these rules:

**Naming rules:**
- Lowercase only
- Use hyphens (not underscores or spaces)
- Be descriptive but concise
- Match team conventions
- Avoid conflicts with existing commands

**Good examples:**
- `review-pr` - Review pull request
- `deploy-staging` - Deploy to staging
- `run-tests` - Execute test suite
- `analyze-logs` - Analyze log files

**Bad examples:**
- `rp` - Too cryptic
- `Review_PR` - Wrong case, underscore
- `review pr` - Space not allowed
- `reviewPullRequest` - camelCase not allowed

**Filename:** `{command-name}.md`

---

### Step 3: Directory Setup (2 min)

**Determine storage location:**

**Project-level (team command):**
```bash
mkdir -p .claude/commands
```

**User-level (personal command):**
```bash
mkdir -p ~/.claude/commands
```

**Verify location:**
```bash
# Project-level
ls .claude/commands/

# User-level
ls ~/.claude/commands/
```

---

### Step 4: Description Writing (5 min)

**Craft a clear description for `/help` output:**

**Description formula:**
```
[Verb] [object] with/by [method/approach]
```

**Best practices:**
- Use imperative voice ("Review PR", not "This reviews PRs")
- Be specific about what it does
- Keep to 1-2 sentences
- Mention key capabilities
- No marketing fluff

**Examples:**

**Good descriptions:**
```yaml
description: Review pull request with structured analysis of code quality, security, and best practices

description: Deploy application to specified environment with pre-deployment validation

description: Run test suite with optional focus area and coverage reporting

description: Analyze log files for errors, warnings, and patterns
```

**Bad descriptions:**
```yaml
description: Helps with PRs
# ❌ Too vague

description: This command will review your pull request and provide feedback
# ❌ Not imperative voice

description: The ultimate deployment solution for all your deployment needs
# ❌ Marketing fluff
```

---

### Step 5: Argument Configuration (10 min)

**Design argument handling:**

#### No Arguments

**Simple prompt expansion:**
```markdown
---
description: Review current git diff
allowed-tools: Bash, Read
---

Review the current git diff and provide structured feedback on:
- Code quality
- Security concerns
- Best practices
```

#### Single Argument ($ARGUMENTS)

**Use case:** One argument, simple capture

```markdown
---
description: Explain the specified concept
argument-hint: <concept>
---

Explain this concept in detail: $ARGUMENTS

Include examples, use cases, and best practices.
```

**Invocation:** `/explain dependency injection`

#### Multiple Positional Arguments ($1, $2, $3)

**Use case:** Multiple arguments appearing in different places

```markdown
---
description: Review pull request with specified focus
argument-hint: <pr-number> <focus-area>
---

Fetch pull request #$1 using:
```bash
gh pr view $1
```

Review the changes with emphasis on $2 aspects.

Provide structured feedback focusing on $2.
```

**Invocation:** `/review-pr 456 security`

#### Optional Arguments with Defaults

**Use case:** Arguments with sensible defaults

```markdown
---
description: Deploy application to specified environment
argument-hint: <environment> [branch]
---

Deploy to $1 environment.

Branch: ${2:-main}

Steps:
1. Checkout branch: ${2:-main}
2. Build for environment: $1
3. Deploy to $1 servers
4. Run smoke tests
```

**Invocations:**
- `/deploy staging feature-123` → branch: feature-123
- `/deploy production` → branch: main (default)

**Syntax:** `${N:-default}` (bash-style default values)

#### Argument Hint Format

**Show expected arguments:**

```yaml
argument-hint: <required> [optional]
argument-hint: <pr-number>
argument-hint: <environment> [branch]
argument-hint: <file-path> <action>
argument-hint: <query> [limit]
```

**Conventions:**
- `<required>` - Must be provided
- `[optional]` - Can be omitted
- `<arg1|arg2>` - Choice between options
- `[args...]` - Variable number

---

### Step 6: Tool Restriction Configuration (5 min)

**Configure tool access:**

#### Read-Only (Analysis, Review)

```yaml
allowed-tools: Read, Grep, Glob
```

**Use for:** Code review, log analysis, research

#### Shell Execution

```yaml
allowed-tools: Bash, Read
```

**Use for:** Running tests, git operations, deployments

#### File Modification

```yaml
allowed-tools: Read, Write, Edit, Bash
```

**Use for:** Code generation, refactoring, file creation

#### Web Access

```yaml
allowed-tools: WebSearch, WebFetch, Read
```

**Use for:** Research, documentation lookup, external data

#### Full Access (Default)

**Omit `allowed-tools` field**

**Use for:** General-purpose commands needing flexibility

---

### Step 7: Command Generation (10-15 min)

**Create the complete command file:**

**Template structure:**

```markdown
---
description: [Clear imperative description]
argument-hint: [Argument format if applicable]
allowed-tools: [Tool list if restricted]
[disable-model-invocation: true  # If destructive]
---

# Main Prompt

[Clear instructions for Claude]

## What to do:

[Step-by-step guidance]

## Expected output:

[What user should see]

## Examples:

[Concrete examples if helpful]
```

**Full example (`deploy.md`):**

```markdown
---
description: Deploy application to specified environment with validation
argument-hint: <environment> [branch]
allowed-tools: Bash, Read
disable-model-invocation: true
---

# Deployment Workflow

Deploy application to $1 environment from branch ${2:-main}.

## Pre-Deployment Validation

1. Verify git status is clean
2. Check branch exists: ${2:-main}
3. Confirm environment is valid: $1
4. Run pre-deployment tests

## Deployment Steps

Execute deployment:

```bash
# Checkout branch
git checkout ${2:-main}

# Pull latest
git pull origin ${2:-main}

# Build for environment
npm run build:$1

# Deploy
npm run deploy:$1

# Verify deployment
npm run verify:$1
```

## Post-Deployment

1. Run smoke tests
2. Verify deployment health
3. Report deployment status
4. Update deployment log

## Rollback Plan

If deployment fails:
1. Stop immediately
2. Preserve error logs
3. Execute rollback: `npm run rollback:$1`
4. Report failure details
```

**Write the file:**

```bash
# Project-level
cat > .claude/commands/{command-name}.md << 'EOF'
[Content]
EOF

# User-level
cat > ~/.claude/commands/{command-name}.md << 'EOF'
[Content]
EOF
```

---

### Step 8: Testing and Validation (10 min)

**Test the command:**

**Test 1: File Validation**
```bash
# Verify file exists
test -f .claude/commands/{command-name}.md && echo "✅ File exists"

# Check YAML frontmatter (if present)
head -20 .claude/commands/{command-name}.md
```

**Test 2: Invocation Test (No Arguments)**
```
User: /{command-name}
Expected: Prompt expands correctly
```

**Test 3: Invocation Test (With Arguments)**
```
User: /{command-name} arg1 arg2
Expected: Arguments substitute correctly
```

**Test 4: Tool Restriction Test**
```
# If allowed-tools is set, verify restrictions work
User: /{command-name}
Expected: Claude only uses specified tools
```

**Test 5: Help Discovery**
```
User: /help
Expected: Command appears in list with description
```

**Common issues:**

**Command not found:**
- Verify file is in correct directory
- Check filename has `.md` extension
- Restart Claude session to reload commands

**Arguments not substituting:**
- Check for typos: `$ARGUMENT` vs `$ARGUMENTS`
- Verify positional numbers: `$1`, `$2`, etc.
- Test with actual invocation

**Tool restrictions not working:**
- Verify `allowed-tools` syntax (comma-separated)
- Check tool names are valid
- Ensure no typos in tool names

---

### Step 9: Documentation (5 min)

**Document the command:**

**For project commands, add to README:**

```markdown
## Claude Commands

### /command-name
**Purpose:** [Brief description]
**Usage:** `/command-name <arg1> [arg2]`
**Examples:**
- `/command-name example1`
- `/command-name example2 optional`

**File:** `.claude/commands/command-name.md`
```

**Commit to git (project commands):**
```bash
git add .claude/commands/{command-name}.md
git commit -m "Add /{command-name} command for [purpose]"
git push
```

**For user commands, add to personal notes**

---

## Quick Reference

### Command Frontmatter Fields

```yaml
---
description: Brief explanation (shown in /help)
argument-hint: <arg1> [arg2]  # Optional
allowed-tools: Tool1, Tool2    # Optional, comma-separated
model: sonnet|opus|haiku       # Optional
disable-model-invocation: true # Optional, prevent auto-invocation
---
```

### Argument Patterns

```markdown
$ARGUMENTS          # All arguments as single string
$1, $2, $3         # Positional arguments
${1:-default}      # Argument with default value
```

### Common Tool Combinations

```yaml
# Read-only analysis
allowed-tools: Read, Grep, Glob

# Shell execution
allowed-tools: Bash, Read

# File modification
allowed-tools: Read, Write, Edit, Bash

# Web research
allowed-tools: WebSearch, WebFetch, Read

# Git operations
allowed-tools: Bash, Read, Write
```

---

## Best Practices

✅ **DO:**
- Use clear, imperative descriptions
- Provide argument hints for discoverability
- Restrict tools when appropriate for safety
- Include examples in command prompt
- Test with actual invocations
- Document in team README (project commands)
- Use `disable-model-invocation` for destructive ops
- Add validation steps for critical commands
- Structure prompts clearly with sections
- Use code blocks for bash commands

❌ **DON'T:**
- Use vague descriptions
- Forget to test argument substitution
- Give excessive permissions (principle of least privilege)
- Make command names cryptic
- Skip documentation
- Allow model invocation for dangerous operations
- Forget about error cases
- Make prompts too complex
- Assume arguments are always provided

---

## Common Patterns

### Pattern 1: Git Workflow Command

```markdown
---
description: Create and push feature branch
argument-hint: <branch-name>
allowed-tools: Bash
---

Create feature branch: $1

```bash
git checkout -b $1
git push -u origin $1
```

Confirm branch created and pushed.
```

### Pattern 2: Testing Command

```markdown
---
description: Run test suite with coverage
argument-hint: [test-path]
allowed-tools: Bash, Read
---

Run tests: ${1:-.}

```bash
pytest ${1:-.} --cov --cov-report=term-missing
```

Analyze results and report:
- Pass/fail summary
- Coverage percentage
- Failed tests details
```

### Pattern 3: Analysis Command

```markdown
---
description: Analyze code quality metrics
allowed-tools: Bash, Read, Grep
---

Analyze code quality:

1. Run linters
2. Check complexity
3. Identify code smells
4. Generate report

Provide actionable recommendations.
```

### Pattern 4: Deployment Command (Safe)

```markdown
---
description: Deploy to staging environment
allowed-tools: Bash, Read
disable-model-invocation: true
---

**DEPLOYMENT TO STAGING**

Pre-checks:
- Verify clean git status
- Confirm tests passing
- Check environment variables

Deploy:
```bash
npm run deploy:staging
```

Post-deployment:
- Run smoke tests
- Verify health checks
- Report status
```

---

## Troubleshooting

### Issue 1: Command Not Found

**Symptoms:** `/command-name` not recognized

**Solutions:**
- Verify file: `test -f .claude/commands/command-name.md`
- Check extension is `.md`
- Restart Claude session
- Check filename for typos

### Issue 2: Arguments Not Working

**Symptoms:** `$1` appears literally instead of substituting

**Solutions:**
- Use `$ARGUMENTS` for single string
- Use `$1`, `$2` for positional
- Test invocation: `/cmd arg1 arg2`
- Check for typos

### Issue 3: Tool Restrictions Ignored

**Symptoms:** Claude uses tools not in `allowed-tools`

**Solutions:**
- Verify syntax: comma-separated, not array
- Check tool names for typos
- Restart session to reload
- Test with explicit tool use

### Issue 4: Description Not in /help

**Symptoms:** Command works but doesn't show in `/help`

**Solutions:**
- Add `description` field to frontmatter
- Restart session
- Check YAML syntax

---

## Success Criteria

A well-built Command has:
- ✅ Clear, imperative description
- ✅ Appropriate argument handling
- ✅ Correct tool restrictions
- ✅ Tested with actual invocations
- ✅ Documented (if project command)
- ✅ Safety measures (if destructive)
- ✅ Clear prompt structure
- ✅ Examples included
- ✅ Error handling considered
- ✅ Team-friendly naming

---

**Remember:** Commands are user-triggered, explicit workflows. They should be clear, safe, and provide repeatable value to users!

## Next Steps

After creating the command:
1. Test it thoroughly with various arguments
2. Get team feedback (project commands)
3. Iterate based on usage
4. Consider related commands (families work well together)
5. Use `/suggest-artifacts` to find more automation opportunities

**Need validation?** Use the `artifact-validator` Skill to check command quality.

**Unsure if Command is right?** Use the `artifact-advisor` Skill to confirm artifact type choice.
