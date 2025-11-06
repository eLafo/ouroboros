# Claude Code Artifact Creation Workflows

## Overview

This document consolidates comprehensive workflows for creating Claude Code artifacts: Skills, Commands, Subagents, and Hooks. Each workflow provides step-by-step guidance with time estimates, decision frameworks, examples, and best practices.

These workflows are designed to be followed sequentially to ensure well-formed, production-ready artifacts that activate correctly, serve their intended purpose, and follow security best practices.

---

## Skill Creation Workflow

## Document Purpose

This context provides a **prescriptive, step-by-step workflow** for creating Claude Code Skills. Follow this process to ensure well-formed Skills that activate correctly and provide value.

**Target audience:** Developers creating Skills for projects or plugins
**Usage:** Follow sequentially when creating any new Skill

---

## Prerequisites

Before starting, ensure you have:
- [ ] Reviewed `knowledge_artifact_comparison_matrix.md` to confirm a Skill is the right artifact type
- [ ] Read `knowledge_skill_specification.md` to understand Skill structure
- [ ] Identified the domain/capability this Skill will provide
- [ ] Access to `.claude/skills/` directory (project or user level)

**Decision checkpoint:** If you're unsure whether to create a Skill vs Command vs Subagent, **STOP** and consult the comparison matrix first.

---

## Workflow Overview

```
1. Pre-Creation Planning (5-10 min)
   ├─ Define purpose and scope
   ├─ Identify activation triggers
   └─ Choose scope (project vs user)

2. Description Crafting (10-15 min) ⭐ CRITICAL
   ├─ List concrete capabilities
   ├─ Include trigger keywords
   └─ Test phrasing variations

3. Directory Setup (2-3 min)
   ├─ Create skill directory
   └─ Initialize SKILL.md

4. YAML Frontmatter Generation (3-5 min)
   ├─ Set name and description
   └─ Configure allowed-tools (if needed)

5. Content Development (15-45 min)
   ├─ Write core methodology
   ├─ Add examples (3-5 minimum)
   └─ Create supporting files

6. Validation (5-10 min)
   ├─ YAML syntax validation
   ├─ Name/directory consistency
   └─ Description quality check

7. Activation Testing (10-15 min) ⭐ CRITICAL
   ├─ Test with direct keywords
   ├─ Test with contextual requests
   └─ Iterate on description

8. Documentation (5 min)
   └─ Add usage notes to project README

Total time: 55-110 minutes for a complete Skill
```

---

## Step 1: Pre-Creation Planning

### 1.1 Define Purpose and Scope

**Answer these questions:**

**Q1: What domain or capability will this Skill provide?**
- Examples: "PDF processing", "API testing", "SQL optimization", "Log analysis"

**Q2: What are the 3-5 core operations this Skill should perform?**
- Be specific: "Extract tables from PDFs", not just "work with PDFs"

**Q3: What file types, technologies, or tools are involved?**
- List explicitly: "PDF files", "REST APIs", "PostgreSQL databases", etc.

**Q4: What problem does this Skill solve that general-purpose Claude cannot?**
- If the answer is "none", you might not need a Skill

**Example planning output:**
```
Domain: API Testing
Core operations:
  1. Send HTTP requests (GET, POST, PUT, DELETE)
  2. Validate response status codes and headers
  3. Compare responses against OpenAPI specifications
  4. Test authentication and authorization
  5. Handle rate limiting and retries
Technologies: REST APIs, HTTP, JSON, OpenAPI/Swagger, OAuth
Problem solved: Structured API testing workflow with OpenAPI validation
```

### 1.2 Identify Activation Triggers

**Critical task:** List keywords users will naturally mention when they need this Skill.

**Brainstorm natural user requests:**
- "Can you test the /users endpoint?"
- "I'm getting a 500 error from the API"
- "Validate this API against the OpenAPI spec"
- "Check if my authentication is working"

**Extract trigger keywords:**
- API, endpoint, HTTP, REST, status code, OpenAPI, authentication, request, response

**Technology-specific terms:**
- curl, Postman, swagger, JSON payload, authorization header

**Action verbs:**
- test, validate, check, debug, send request, call endpoint

**File patterns:**
- .json, swagger.yaml, openapi.json

**Output:** A list of 10-20 keywords that should be included in the description.

### 1.3 Choose Scope (Project vs User)

**Project Skill** (`.claude/skills/`):
- ✅ Team-shared workflows
- ✅ Project-specific standards
- ✅ Domain knowledge tied to this codebase
- ✅ Committed to git

**User Skill** (`~/.claude/skills/`):
- ✅ Personal productivity tools
- ✅ Applicable across all projects
- ✅ Not for team sharing
- ✅ User-specific preferences

**Decision:** Choose project scope unless the Skill is purely personal.

---

## Step 2: Description Crafting ⭐ CRITICAL

**This is the most important step.** The description directly controls when Claude activates the Skill.

### 2.1 Description Formula

Use this structure:

```
[ACTION VERBS] [CAPABILITIES] using [TECHNOLOGIES/TOOLS].
[ADDITIONAL CAPABILITIES].
Use when [TRIGGER SCENARIOS].
```

**Components:**

1. **Action verbs** (first sentence):
   - Extract, analyze, test, validate, generate, debug, optimize, convert, etc.

2. **Capabilities** (what it does):
   - Specific operations, not vague descriptions

3. **Technologies/tools** (domain context):
   - File types, frameworks, libraries, protocols

4. **Trigger scenarios** (when to use):
   - User situations that should activate this Skill

### 2.2 Description Examples

**Example 1: API Tester**
```yaml
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes, headers, and payloads. Compare against OpenAPI specifications, test authentication, handle rate limits. Use for API development, integration testing, or debugging HTTP services.
```

**Breakdown:**
- Action verbs: Test, send, validate, check, compare
- Capabilities: HTTP requests, response validation, OpenAPI comparison
- Technologies: REST APIs, HTTP, OpenAPI
- Triggers: API development, integration testing, debugging HTTP services

---

**Example 2: PDF Processor**
```yaml
description: Extract text, images, and tables from PDFs. Fill PDF forms, merge documents, convert between formats (PDF↔Word). Use when working with PDF files or document processing tasks.
```

**Breakdown:**
- Action verbs: Extract, fill, merge, convert
- Capabilities: Text/image/table extraction, form filling, merging, conversion
- Technologies: PDF files, Word documents
- Triggers: Working with PDF files, document processing

---

**Example 3: SQL Optimizer**
```yaml
description: Analyze and optimize SQL queries for PostgreSQL and MySQL. Explain execution plans, suggest indexes, identify N+1 problems, recommend query rewrites. Use when working with database performance issues or query optimization.
```

**Breakdown:**
- Action verbs: Analyze, optimize, explain, suggest, identify, recommend
- Capabilities: Query analysis, index suggestions, N+1 detection
- Technologies: SQL, PostgreSQL, MySQL
- Triggers: Database performance, query optimization

---

**Example 4: Log Analyzer**
```yaml
description: Analyze application logs for errors, warnings, patterns, and anomalies. Parse structured logs (JSON, syslog), identify error trends, correlate events. Use when debugging issues, investigating incidents, or monitoring log files.
```

**Breakdown:**
- Action verbs: Analyze, parse, identify, correlate
- Capabilities: Error detection, pattern analysis, event correlation
- Technologies: Log files, JSON logs, syslog
- Triggers: Debugging, incident investigation, monitoring

---

### 2.3 Description Anti-Patterns

**❌ Too vague:**
```yaml
description: Helps with APIs
```
**Problem:** No specific capabilities, no trigger keywords

**❌ Too narrow:**
```yaml
description: Extracts tables from PDF files using tabula-py
```
**Problem:** Overly specific, won't activate for broader PDF tasks

**❌ Too broad:**
```yaml
description: Processes files and data for various tasks
```
**Problem:** Too generic, will activate incorrectly or not at all

**❌ Missing technologies:**
```yaml
description: Test web services and validate responses
```
**Problem:** Lacks specific terms (REST, HTTP, API) users will mention

**❌ No trigger scenarios:**
```yaml
description: Extract text and tables from PDFs, fill forms, merge documents
```
**Problem:** Missing "Use when..." clause that helps Claude decide activation

---

### 2.4 Description Validation Checklist

Before proceeding, verify your description:

- [ ] Includes 3-5 action verbs
- [ ] Lists specific capabilities (not vague "helps with")
- [ ] Mentions technologies, file types, or tools explicitly
- [ ] Includes "Use when..." trigger scenarios
- [ ] Contains keywords users naturally mention
- [ ] Is 50-200 words (not too short, not too long)
- [ ] Under 1024 characters (hard limit)
- [ ] Reads naturally when spoken aloud

**Test:** Read the description aloud. Does it sound like something you'd tell a colleague?

---

## Step 3: Directory Setup

### 3.1 Create Skill Directory

**For project Skills:**
```bash
mkdir -p .claude/skills/skill-name
```

**For user Skills:**
```bash
mkdir -p ~/.claude/skills/skill-name
```

**Naming rules:**
- Lowercase only
- Hyphens for separation (no underscores, no spaces)
- Descriptive and concise
- Maximum 64 characters

**Good names:**
- `api-tester`
- `pdf-processor`
- `sql-optimizer`
- `log-analyzer`

**Bad names:**
- `API_Tester` (uppercase, underscore)
- `api tester` (space)
- `apiTester` (camelCase)
- `my-really-super-long-skill-name-that-describes-everything-it-does` (too long)

### 3.2 Initialize SKILL.md

Create the main Skill file:

```bash
touch .claude/skills/skill-name/SKILL.md
```

---

## Step 4: YAML Frontmatter Generation

### 4.1 Basic Structure

Open `SKILL.md` and add YAML frontmatter:

```yaml
---
name: skill-name
description: [Your crafted description from Step 2]
---
```

**Requirements:**
- Opening `---` on line 1
- `name` must match directory name exactly
- `description` from Step 2 (max 1024 chars)
- Closing `---` after fields
- Blank line after closing `---` before markdown content

### 4.2 Adding Tool Restrictions (Optional)

If your Skill should be **read-only** or have **limited tool access**:

```yaml
---
name: skill-name
description: [Your description]
allowed-tools: Read, Grep, Bash
---
```

**Common tool restriction patterns:**

**Read-only (analysis, inspection):**
```yaml
allowed-tools: Read, Grep, Glob
```

**Read-only with limited command execution:**
```yaml
allowed-tools: Read, Grep, Bash
```

**File manipulation without execution:**
```yaml
allowed-tools: Read, Write, Edit, Grep, Glob
```

**Research and information gathering:**
```yaml
allowed-tools: WebSearch, WebFetch, Read
```

**If in doubt, omit `allowed-tools`** - Skill will inherit all available tools.

### 4.3 Complete Example

```yaml
---
name: api-tester
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes, headers, and payloads. Compare against OpenAPI specifications, test authentication, handle rate limits. Use for API development, integration testing, or debugging HTTP services.
allowed-tools: Bash, Read, Write, WebFetch
---

[Markdown content starts here]
```

---

## Step 5: Content Development

### 5.1 Core Methodology Structure

After the YAML frontmatter, write the Skill content using this structure:

```markdown
# [Skill Name]: [Brief Tagline]

## Overview

[1-2 paragraph summary of what this Skill does and why it's useful]

## Core Capabilities

[Bulleted list of main capabilities]

## Methodology / Workflow

[Step-by-step process for using this Skill]

### Step 1: [First Step]
[Detailed instructions]

### Step 2: [Second Step]
[Detailed instructions]

[Continue for all major steps...]

## Examples

[3-5 concrete examples showing real usage]

### Example 1: [Scenario Name]
[Complete example with inputs and outputs]

### Example 2: [Scenario Name]
[Complete example with inputs and outputs]

[Continue for all examples...]

## Common Patterns

[Frequently used patterns or shortcuts]

## Troubleshooting

[Common issues and solutions]

## Supporting Files

[References to supporting documentation if applicable]
```

### 5.2 Writing Effective Examples (3-5 Required)

**Each example must include:**
1. Clear scenario description
2. Sample input (code, command, or data)
3. Expected output or result
4. Explanation of what happened

**Example format:**

```markdown
### Example 1: Testing User Creation Endpoint

**Scenario:** Test POST /api/users endpoint with valid payload

**Input:**
```bash
curl -X POST "https://api.example.com/users" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{
    "email": "test@example.com",
    "name": "Test User"
  }'
```

**Expected output:**
- Status: 201 Created
- Response body contains user ID
- Location header with user resource URL

**Validation:**
```bash
# Check status code
# Verify response structure
# Confirm Location header format
```

**What to check:**
- ✅ Status code is 201 (not 200)
- ✅ Response includes `id` field
- ✅ Email matches input
- ✅ Location header: `https://api.example.com/users/{id}`
```

**Minimum 3 examples, recommended 5.**

### 5.3 Supporting Files (Optional)

For complex Skills, create supporting documentation:

**Structure:**
```
.claude/skills/skill-name/
├── SKILL.md              # Main workflow
├── examples.md           # Extended examples
├── reference.md          # API/technical reference
└── templates/
    ├── template1.json
    └── script.py
```

**When to create supporting files:**
- Skill content exceeds ~500 lines
- Need detailed API reference documentation
- Have reusable templates or scripts
- Want progressive disclosure (load detail only when needed)

**In SKILL.md, reference supporting files:**
```markdown
For detailed API reference, see `reference.md`.
For more examples, see `examples.md`.
For template files, see `templates/` directory.
```

---

## Step 6: Validation

### 6.1 YAML Syntax Validation

**Manual checklist:**
- [ ] Opening `---` on line 1
- [ ] Closing `---` before markdown content
- [ ] `name` field present and matches directory name
- [ ] `description` field present and ≤1024 characters
- [ ] If `allowed-tools` present, uses comma-separated format (not array)
- [ ] No tab characters (use spaces only)
- [ ] No smart quotes (use straight quotes: `"` not `"` or `"`)

**Command-line validation (if available):**
```bash
# Check YAML syntax
python3 -c "import yaml; yaml.safe_load(open('.claude/skills/skill-name/SKILL.md').read().split('---')[1])"
```

**Expected:** No output = valid YAML

**If errors occur:** Fix syntax issues and re-validate.

### 6.2 Name/Directory Consistency Check

```bash
# Verify directory name matches YAML name field
DIRNAME=$(basename $(pwd))
YAMLNAME=$(grep "^name:" SKILL.md | cut -d: -f2 | xargs)

if [ "$DIRNAME" == "$YAMLNAME" ]; then
  echo "✅ Name matches"
else
  echo "❌ Mismatch: directory=$DIRNAME, yaml=$YAMLNAME"
fi
```

### 6.3 Description Quality Check

Review your description against these criteria:

**Scoring (each worth 1 point, target: 8/10):**
- [ ] Includes specific action verbs (extract, analyze, test, etc.)
- [ ] Lists concrete capabilities (not "helps with")
- [ ] Mentions file types or technologies explicitly
- [ ] Includes "Use when..." trigger scenarios
- [ ] Contains 10+ trigger keywords
- [ ] Reads naturally (not keyword-stuffed)
- [ ] Is 50-200 words
- [ ] Under 1024 characters
- [ ] No vague terms ("various", "multiple", "helps with")
- [ ] Passes "would I tell a colleague this?" test

**If score < 8:** Revise description and re-check.

---

## Step 7: Activation Testing ⭐ CRITICAL

**Most important testing phase.** This verifies Claude will actually use your Skill.

### 7.1 Test Setup

**Option A: Use /claude command (if available)**
```bash
claude --skills .claude/skills
```

**Option B: Restart Claude Code session**
- Save SKILL.md
- Exit and restart Claude Code
- New session will load the Skill

### 7.2 Test 1: Direct Keyword Activation

Ask Claude using keywords directly from your description:

**Example (API Tester Skill):**
```
User: "Can you test the /users endpoint of my REST API?"
```

**Expected:** Claude activates `api-tester` Skill

**Check:** Look for Skill invocation message or behavior matching Skill methodology

**If Skill doesn't activate:**
- Add more specific keywords to description
- Ensure keywords match what users naturally say
- Try more direct phrasing

### 7.3 Test 2: Contextual Activation

Provide context that should trigger the Skill without explicit keywords:

**Example (PDF Processor Skill):**
```
User: "Here's my quarterly-report.pdf. What tables are in it?"
```

**Expected:** Claude activates `pdf-processor` Skill

**If Skill doesn't activate:**
- Add file extension patterns to description (.pdf, .json, etc.)
- Include more contextual trigger phrases
- Make domain language more explicit

### 7.4 Test 3: File-Based Activation

Attach a file that should trigger the Skill:

**Example (Log Analyzer Skill):**
```
User: "Analyze this for errors" [attaches application.log]
```

**Expected:** Claude activates `log-analyzer` Skill

**If Skill doesn't activate:**
- Add file type mentions to description ("log files", "error logs")
- Include common file naming patterns
- Add file extension patterns

### 7.5 Test 4: Boundary Testing

Verify Skill DOESN'T activate when it shouldn't:

**Example (SQL Optimizer Skill):**
```
User: "Can you test my REST API?"
```

**Expected:** SQL Optimizer should NOT activate (API testing is different domain)

**If Skill activates incorrectly:**
- Description is too broad
- Remove generic keywords that overlap with other domains
- Make description more specific to exact use case

### 7.6 Iteration Loop

**If activation tests fail:**

1. **Analyze the gap:** What keywords did you use vs what's in description?
2. **Update description:** Add missing trigger terms
3. **Re-validate:** Check YAML syntax, character limit
4. **Re-test:** Repeat activation tests
5. **Iterate until:** Skill activates reliably for intended use cases

**Typical iterations: 2-4 rounds**

**Success criteria:**
- ✅ Activates with direct keyword mentions (Test 1)
- ✅ Activates with contextual requests (Test 2)
- ✅ Activates with relevant file types (Test 3)
- ✅ Does NOT activate for unrelated requests (Test 4)

---

## Step 8: Documentation

### 8.1 Add Usage Notes to Project README

If this is a project Skill, document it in your project's README or SKILLS.md:

```markdown
## Available Skills

### API Tester
**Purpose:** Test REST APIs with structured validation

**Activation:** Mention "API", "endpoint", "HTTP request", or attach OpenAPI spec

**Example usage:**
- "Test the /users endpoint"
- "Validate my API against the OpenAPI spec"
- "Check if authentication is working"

**File:** `.claude/skills/api-tester/SKILL.md`
```

### 8.2 Team Communication (Project Skills Only)

If creating a shared team Skill:

1. **Commit to git:**
   ```bash
   git add .claude/skills/skill-name/
   git commit -m "Add [skill-name] Skill for [purpose]"
   git push
   ```

2. **Notify team:**
   - Send message explaining new Skill
   - Share example activation phrases
   - Link to documentation

3. **Demonstrate usage:**
   - Show example session using the Skill
   - Highlight trigger keywords
   - Explain when to use it

---

## Complete Example: Creating a Log Analyzer Skill

### Step 1: Planning

**Domain:** Log analysis and debugging
**Core operations:**
1. Parse log files (JSON, syslog, plain text)
2. Find errors and exceptions
3. Identify patterns and trends
4. Correlate events
5. Generate summary reports

**Technologies:** Log files, JSON logs, syslog, grep, bash
**Trigger keywords:** log, error, exception, debug, investigate, analyze logs

**Scope:** Project (team will use for debugging)

### Step 2: Description

**First draft:**
```yaml
description: Analyze logs for errors and patterns
```

**Problem:** Too vague, missing trigger keywords

**Second draft:**
```yaml
description: Analyze application logs for errors, warnings, patterns, and anomalies. Parse structured logs (JSON, syslog), identify error trends, correlate events. Use when debugging issues, investigating incidents, or monitoring log files.
```

**✅ Better:** Specific capabilities, technologies, and trigger scenarios

### Step 3: Directory Setup

```bash
mkdir -p .claude/skills/log-analyzer
cd .claude/skills/log-analyzer
touch SKILL.md
```

### Step 4: YAML Frontmatter

```yaml
---
name: log-analyzer
description: Analyze application logs for errors, warnings, patterns, and anomalies. Parse structured logs (JSON, syslog), identify error trends, correlate events. Use when debugging issues, investigating incidents, or monitoring log files.
allowed-tools: Read, Grep, Bash
---
```

**Note:** Read-only tools (no Write or Edit) since this is analysis only.

### Step 5: Content Development

```markdown
# Log Analyzer Skill

## Overview

This Skill helps analyze application logs to find errors, identify patterns, and debug issues. It supports multiple log formats including plain text, JSON lines, and syslog.

## Core Capabilities

1. **Error Detection:** Find errors, exceptions, and warnings
2. **Pattern Analysis:** Identify recurring issues and trends
3. **Event Correlation:** Connect related events across logs
4. **Format Parsing:** Handle JSON, syslog, and plain text logs
5. **Summary Generation:** Create actionable debugging reports

## Workflow

### Step 1: Identify Log Format

First, examine a sample to determine format:

```bash
head -20 application.log
```

Common formats:
- Plain text with timestamps
- JSON lines (one object per line)
- Syslog format
- Custom application format

### Step 2: Search for Errors

Use grep to find error patterns:

```bash
# Common error keywords
grep -i "error\|exception\|fatal\|critical" application.log

# HTTP errors
grep -E "HTTP/(1\.1|2) [45][0-9]{2}" access.log

# Stack traces
grep -A 10 "Exception" app.log
```

### Step 3: Analyze Frequency and Timing

Count occurrences and identify trends:

```bash
# Count errors by type
grep -i "error" application.log | sort | uniq -c | sort -nr

# Errors by hour
grep "error" application.log | cut -d' ' -f1-2 | uniq -c
```

### Step 4: Correlate Events

Find related events around the error time:

```bash
# Events within 1 minute of error
ERROR_TIME="2025-11-05 14:32:15"
grep -E "$ERROR_TIME|$(date -d "$ERROR_TIME + 1 min" +"%Y-%m-%d %H:%M")" app.log
```

### Step 5: Generate Summary

Provide actionable report:
- Total errors by severity
- Error timeline (frequency over time)
- Root cause hypothesis
- Recommended next steps

## Examples

### Example 1: Finding Critical Errors

**Scenario:** Application is failing intermittently

**Command:**
```bash
grep -i "critical\|fatal" /var/log/application.log
```

**Output:**
```
2025-11-05 14:32:15 CRITICAL Database connection failed: timeout after 30s
2025-11-05 14:35:22 CRITICAL Database connection failed: timeout after 30s
2025-11-05 14:38:41 CRITICAL Database connection failed: timeout after 30s
```

**Analysis:**
- Critical database timeouts occurring every 3-4 minutes
- Consistent 30-second timeout
- Suggests database server overload or network issue

**Recommendation:** Check database server load and network connectivity

[... more examples ...]

## Troubleshooting

**Issue:** Log format not recognized
**Solution:** Examine raw file and identify timestamp pattern, then customize grep patterns

**Issue:** Too many results
**Solution:** Narrow time range or filter by specific error types
```

### Step 6: Validation

```bash
# Check YAML syntax
head -10 SKILL.md | grep -E "^(name|description):"

# Verify name matches directory
basename $(pwd)  # Should output: log-analyzer
grep "^name:" SKILL.md  # Should output: name: log-analyzer
```

**✅ Validation passed**

### Step 7: Activation Testing

**Test 1: Direct keywords**
```
User: "Can you analyze this log file for errors?"
```
**Expected:** ✅ log-analyzer activates

**Test 2: Contextual request**
```
User: "My application is crashing. Here's the log: [paste]"
```
**Expected:** ✅ log-analyzer activates

**Test 3: File-based**
```
User: "Debug this" [attaches application.log]
```
**Expected:** ✅ log-analyzer activates

**Test 4: Boundary**
```
User: "Can you test my REST API?"
```
**Expected:** ✅ log-analyzer does NOT activate (unrelated)

**✅ All tests passed**

### Step 8: Documentation

Added to project README:

```markdown
## Claude Skills

### Log Analyzer
**Purpose:** Analyze application logs for errors and debugging

**Trigger phrases:**
- "Analyze this log file"
- "Find errors in the logs"
- "Debug this issue using logs"

**File:** `.claude/skills/log-analyzer/SKILL.md`
```

**✅ Skill creation complete!**

---

## Common Pitfalls and Solutions

### Pitfall 1: Skill Never Activates

**Symptoms:** Claude doesn't use the Skill even for relevant requests

**Common causes:**
- Description too vague or generic
- Missing key trigger words users naturally say
- YAML syntax error preventing Skill from loading

**Solutions:**
1. Add 10+ concrete trigger keywords to description
2. Test with explicit mentions of technology names
3. Validate YAML syntax
4. Restart Claude Code session to reload Skills

### Pitfall 2: Skill Activates Too Broadly

**Symptoms:** Skill activates for unrelated requests, interfering with other workflows

**Common causes:**
- Description too broad
- Generic keywords that match many domains

**Solutions:**
1. Narrow description to specific use cases
2. Remove generic terms ("data", "files", "processing")
3. Add domain-specific vocabulary
4. Test boundary cases (ensure it doesn't activate incorrectly)

### Pitfall 3: YAML Parsing Errors

**Symptoms:** Skill doesn't appear in Claude, YAML errors in logs

**Common causes:**
- Missing opening or closing `---`
- Tab characters instead of spaces
- Smart quotes instead of straight quotes
- Name mismatch with directory

**Solutions:**
1. Validate YAML with Python or online validator
2. Replace tabs with spaces
3. Use plain text editor (not Word or rich text)
4. Ensure name field exactly matches directory name

### Pitfall 4: Tool Restrictions Too Limiting

**Symptoms:** Skill can't complete tasks, requests tools it doesn't have

**Common causes:**
- `allowed-tools` too restrictive for Skill's purpose
- Missing necessary tools (e.g., Write for saving outputs)

**Solutions:**
1. Review what tools the Skill methodology requires
2. Add missing tools to `allowed-tools`
3. Or remove `allowed-tools` entirely (inherit all tools)
4. Test Skill workflow end-to-end

### Pitfall 5: Examples Too Generic

**Symptoms:** Users don't understand how to trigger or use the Skill

**Common causes:**
- Placeholder examples with "TODO" or "[FILL IN]"
- Examples don't show realistic inputs/outputs
- Missing context or explanation

**Solutions:**
1. Use real, specific examples from actual use cases
2. Show complete inputs and expected outputs
3. Include 3-5 examples minimum
4. Add explanations of what happened and why

---

## Quality Checklist

Before considering your Skill complete:

**Structure:**
- [ ] Directory name is lowercase with hyphens
- [ ] SKILL.md exists with YAML frontmatter
- [ ] Name matches directory exactly
- [ ] Description is specific and ≤1024 characters

**Content:**
- [ ] Core methodology clearly explained
- [ ] 3-5 concrete examples included
- [ ] Examples show real inputs and outputs
- [ ] Supporting files created if needed

**Activation:**
- [ ] Description includes 10+ trigger keywords
- [ ] Tested with direct keyword mentions (passes)
- [ ] Tested with contextual requests (passes)
- [ ] Tested boundary cases (doesn't activate incorrectly)
- [ ] Iterated description based on test results

**Validation:**
- [ ] YAML syntax validated (no errors)
- [ ] Tool restrictions tested (if applicable)
- [ ] Documented in project README (if project Skill)
- [ ] Committed to git (if project Skill)

**Target:** 10/10 checklist items completed

---

## Command Creation Workflow

## Document Purpose

This context provides a **prescriptive workflow** for creating Claude Code slash commands. Follow this process to ensure well-formed commands that serve team workflows effectively.

**Target audience:** Developers creating repeatable team workflows
**Usage:** Follow sequentially when creating any new command

---

## Prerequisites

Before starting:
- [ ] Reviewed `knowledge_artifact_comparison_matrix.md` to confirm a command is the right choice
- [ ] Read `knowledge_command_specification.md` to understand command structure
- [ ] Identified the workflow this command will automate
- [ ] Decided on project vs user scope

**Decision checkpoint:** Commands are for EXPLICIT user actions. If Claude should automatically detect when to act, use a Skill instead.

---

## Workflow Overview

```
1. Planning (5 min)
   └─ Define purpose, arguments, and scope

2. Argument Pattern Design (5 min)
   └─ Choose $ARGUMENTS vs $1,$2,$3

3. File Creation (2 min)
   └─ Create command file

4. Frontmatter Configuration (3 min)
   └─ Add metadata

5. Prompt Development (10-20 min)
   └─ Write command content

6. Testing (5-10 min)
   └─ Test with various arguments

7. Documentation (3 min)
   └─ Add to project README

Total: 33-48 minutes per command
```

---

## Step 1: Planning

### Define Purpose

**What workflow does this command automate?**

Examples:
- "Review pull request with structured analysis"
- "Deploy to specified environment"
- "Run tests for a specific file"
- "Generate API documentation"

**Who will use it?**
- Entire team → Project command (`.claude/commands/`)
- Just you → User command (`~/.claude/commands/`)

### Identify Arguments

**What inputs does the command need?**

**Example: PR Review Command**
- Required: PR number
- Optional: Focus area (security, performance, etc.)

**Example: Deployment Command**
- Required: Environment (staging/production)
- Optional: Branch name, skip tests flag

**Argument decision tree:**
```
Does command need inputs?
├─ NO → Simple command, no arguments
└─ YES
   ├─ All arguments used together in one place?
   │  └→ Use $ARGUMENTS
   └─ Arguments appear in multiple places?
      └→ Use $1, $2, $3, etc.
```

---

## Step 2: Argument Pattern Design

### Pattern A: $ARGUMENTS (All Together)

**When to use:**
- All arguments form a single concept
- Arguments appear once in the prompt
- No need to reference arguments separately

**Example:** Research command
```
/research How does authentication work in microservices?
```

All arguments = the research question

**Template:**
```markdown
---
argument-hint: <your-question-here>
---

Research and answer this question: $ARGUMENTS

[Rest of prompt]
```

### Pattern B: Positional ($1, $2, $3)

**When to use:**
- Arguments have distinct purposes
- Arguments appear in different parts of prompt
- Need to reference arguments separately

**Example:** PR review command
```
/review-pr 456 security
```

- $1 = PR number (456)
- $2 = Focus area (security)

**Template:**
```markdown
---
argument-hint: <pr-number> <focus-area>
---

Fetch PR #$1 using: `gh pr view $1`

Review changes focusing on $2 aspects.

[Rest of prompt emphasizing $2]
```

### Pattern C: Optional Arguments

**Use bash parameter expansion:**
```markdown
---
argument-hint: <environment> [branch]
---

Deploy to $1 environment
Branch: ${2:-main}
```

`${2:-main}` means "use $2 if provided, otherwise use 'main'"

---

## Step 3: File Creation

### Create Command File

**For project commands:**
```bash
touch .claude/commands/command-name.md
```

**For user commands:**
```bash
touch ~/.claude/commands/command-name.md
```

**Naming rules:**
- Lowercase only
- Hyphens for word separation
- Descriptive verb-noun pattern
- `.md` extension required

**Good names:**
- `review-pr.md`
- `deploy-staging.md`
- `run-tests.md`
- `analyze-logs.md`

**Bad names:**
- `Review_PR.md` (uppercase, underscore)
- `reviewPR.md` (camelCase)
- `rp.md` (unclear abbreviation)

---

## Step 4: Frontmatter Configuration

### Basic Frontmatter

```yaml
---
description: Brief description of what command does
argument-hint: <required-arg> [optional-arg]
---
```

### Add Tool Restrictions (Recommended)

```yaml
---
description: Review code changes with structured feedback
argument-hint: <file-path>
allowed-tools: Bash, Read, Grep
---
```

**Common tool patterns:**

**Read-only analysis:**
```yaml
allowed-tools: Read, Grep, Glob
```

**Shell execution:**
```yaml
allowed-tools: Bash, Read
```

**Full file manipulation:**
```yaml
allowed-tools: Read, Write, Edit, Bash
```

**Web research:**
```yaml
allowed-tools: WebSearch, WebFetch, Read, Write
```

### Add Safety Controls (For Destructive Commands)

```yaml
---
description: Deploy to production
disable-model-invocation: true  # Prevents Claude from auto-running this
model: sonnet
---
```

---

## Step 5: Prompt Development

### Structure Template

```markdown
---
[frontmatter here]
---

[Brief context/overview]

[Main instructions with arguments]

[Step-by-step process if applicable]

[Expected output format]
```

### Example: Simple Command (No Arguments)

**File:** `review.md`
```markdown
---
description: Review current git changes
allowed-tools: Bash, Read
---

Review the current git diff:

```bash
git diff
```

Provide structured feedback on:
1. Code quality and maintainability
2. Security concerns
3. Best practices compliance
4. Suggested improvements
```

### Example: Command with Arguments

**File:** `test-file.md`
```markdown
---
description: Run tests for a specific file
argument-hint: <file-path>
allowed-tools: Bash
---

Running tests for: $1

```bash
# Convert source file to test file path
TEST_FILE=$(echo "$1" | sed 's/src/tests/' | sed 's/\.js$/.test.js/')

if [ -f "$TEST_FILE" ]; then
    npm test -- "$TEST_FILE"
else
    echo "Test file not found: $TEST_FILE"
    exit 1
fi
```

Analyze test results and suggest improvements if tests fail.
```

### Example: Complex Multi-Step Command

**File:** `deploy.md`
```markdown
---
description: Deploy to specified environment
argument-hint: <environment> [branch]
allowed-tools: Bash
disable-model-invocation: true
---

🚀 **Deployment to $1**

Branch: ${2:-main}

## Pre-Deployment Checklist

Before deploying, verify:
- [ ] All tests passing
- [ ] Code reviewed and approved
- [ ] Environment variables configured
- [ ] Database migrations ready

## Deployment Steps

```bash
# Validate environment
if [[ ! "$1" =~ ^(staging|production)$ ]]; then
    echo "❌ Invalid environment: $1"
    exit 1
fi

# Checkout and update branch
git checkout "${2:-main}"
git pull origin "${2:-main}"

# Run tests
echo "Running test suite..."
npm test || { echo "❌ Tests failed!"; exit 1; }

# Build
echo "Building for $1..."
npm run build:$1

# Deploy
echo "Deploying to $1..."
npm run deploy:$1

echo "✅ Deployment complete!"
```

## Post-Deployment

Verify deployment:
```bash
curl https://$1.example.com/health
npm run status:$1
```

Monitor logs for 5 minutes:
```bash
npm run logs:$1 --follow
```
```

---

## Step 6: Testing

### Test Checklist

- [ ] Test with no arguments (if applicable)
- [ ] Test with required arguments only
- [ ] Test with all arguments (required + optional)
- [ ] Test with special characters in arguments
- [ ] Test with very long arguments
- [ ] Test with incorrect number of arguments

### Test Protocol

**Test 1: Basic Invocation**
```
/command-name arg1 arg2
```

**Expected:** Command expands correctly with arguments substituted

**Test 2: Special Characters**
```
/command-name "arg with spaces" 'arg-with-quotes'
```

**Expected:** Arguments preserved correctly

**Test 3: Optional Arguments**
```
/command-name required-only
```

**Expected:** Defaults applied for optional arguments

**Test 4: Error Handling**
```
/command-name
```

**Expected:** Clear error if required arguments missing

### Validation Script

```bash
#!/bin/bash
# test-command.sh

COMMAND_FILE=".claude/commands/my-command.md"

# Test YAML validity
echo "Testing YAML frontmatter..."
python3 -c "
import yaml
with open('$COMMAND_FILE') as f:
    content = f.read()
    if content.startswith('---'):
        parts = content.split('---', 2)
        yaml.safe_load(parts[1])
        print('✅ YAML valid')
"

# Test argument references
echo "Checking argument usage..."
if grep -q '\$ARGUMENTS\|\$[0-9]' "$COMMAND_FILE"; then
    echo "✅ Uses arguments"

    # Check for argument-hint
    if grep -q "^argument-hint:" "$COMMAND_FILE"; then
        echo "✅ Has argument-hint"
    else
        echo "⚠️ Missing argument-hint (recommended)"
    fi
fi

echo "✅ Validation complete"
```

---

## Step 7: Documentation

### Add to Project README

**For team commands, document in project:**

```markdown
## Claude Commands

### /review
**Purpose:** Review current git changes with structured feedback
**Usage:** `/review`
**Tools:** Bash, Read

### /deploy
**Purpose:** Deploy to specified environment
**Usage:** `/deploy <environment> [branch]`
**Example:** `/deploy staging feature-123`
**⚠️ Note:** Manual invocation only (not auto-invoked by Claude)

### /test-file
**Purpose:** Run tests for a specific file
**Usage:** `/test-file <file-path>`
**Example:** `/test-file src/auth/login.js`
```

### Commit to Git (Project Commands)

```bash
git add .claude/commands/
git commit -m "Add [command-name] command for [purpose]"
git push
```

---

## Complete Examples

### Example 1: Code Review Command

**File:** `.claude/commands/review-pr.md`

```markdown
---
description: Review pull request with structured analysis
argument-hint: <pr-number> [focus-area]
allowed-tools: Bash, Read, Grep
---

# Pull Request Review: #$1

Focus area: ${2:-general}

## Fetch PR

```bash
gh pr view $1 --json title,body,files
```

## Review Criteria

Analyze changes focusing on ${2:-general} aspects:

**Code Quality:**
- Readability and maintainability
- Naming conventions
- Code organization

**Security:**
- Input validation
- Authentication/authorization
- Potential vulnerabilities

**Best Practices:**
- Error handling
- Testing coverage
- Documentation

**${2:-General} Focus:**
Provide detailed analysis of ${2:-all} aspects with specific examples from the changes.

## Output Format

Provide review in this structure:
1. Summary (2-3 sentences)
2. Detailed findings (grouped by category)
3. Recommendations (prioritized)
4. Approval status (approve / request changes / comment)
```

**Usage:**
```
/review-pr 456
/review-pr 456 security
/review-pr 789 performance
```

---

### Example 2: Multi-Step Workflow

**File:** `.claude/commands/feature-complete.md`

```markdown
---
description: Complete feature branch and create pull request
argument-hint: <feature-name>
allowed-tools: Bash, Read, Write
---

# Completing Feature: $1

## Step 1: Verify Tests Pass

```bash
echo "Running test suite..."
npm test || { echo "❌ Tests must pass before completing feature"; exit 1; }
```

## Step 2: Update Documentation

Check if documentation needs updates:

```bash
git diff main...HEAD --name-only | grep -E '\.md$|docs/'
```

If changes affect public APIs or behavior, update relevant docs.

## Step 3: Create Pull Request

```bash
# Get feature info
CURRENT_BRANCH=$(git branch --show-current)
git diff main...HEAD --stat

# Create PR
gh pr create \
  --title "Feature: $1" \
  --body "$(cat <<EOF
## Feature: $1

### Changes
- [List key changes]

### Testing
- [x] Unit tests passing
- [x] Integration tests passing
- [ ] Manual testing complete

### Documentation
- [ ] README updated
- [ ] API docs updated (if applicable)
- [ ] Changelog updated

### Checklist
- [x] Code follows project conventions
- [x] Tests added/updated
- [ ] Code reviewed
- [ ] Ready to merge
EOF
)" \
  --web
```

## Step 4: Notify Team

```bash
echo "✅ Pull request created for feature: $1"
echo "Next: Request code review from team"
```
```

**Usage:** `/feature-complete user-authentication`

---

## Common Patterns

### Pattern 1: Conditional Logic

```markdown
```bash
if [ "$1" == "production" ]; then
    echo "⚠️ Production requires extra approval"
    # Additional checks
fi
```
```

### Pattern 2: Default Values

```markdown
Environment: ${1:-staging}
Branch: ${2:-main}
Tag: ${3:-latest}
```

### Pattern 3: File References

```markdown
Review @$1 considering architecture in @docs/ARCHITECTURE.md
```

### Pattern 4: Chained Commands

```markdown
After completion, run: /deploy staging $1
```

---

## Troubleshooting

### Issue 1: Command Not Found

**Symptoms:** `/command` not recognized

**Fix:**
- Verify file is `.md`
- Restart Claude session
- Check file location (`.claude/commands/`)

### Issue 2: Arguments Not Substituted

**Symptoms:** `$1` appears literally

**Fix:**
- Ensure arguments provided during invocation
- Check syntax: `$1` not `${1}` for basic substitution

### Issue 3: Tool Restrictions Block Operation

**Symptoms:** "Tool not allowed" error

**Fix:**
```yaml
# Add necessary tools
allowed-tools: Bash, Read, Write, Grep
```

---

## Quality Checklist

Before considering command complete:

**Structure:**
- [ ] File named correctly (lowercase, hyphens, `.md`)
- [ ] Frontmatter valid YAML
- [ ] Description clear and concise

**Arguments:**
- [ ] Argument pattern chosen correctly
- [ ] `argument-hint` matches actual usage
- [ ] Optional arguments have defaults

**Content:**
- [ ] Purpose clearly explained
- [ ] Steps logically ordered
- [ ] Code blocks properly formatted
- [ ] Error handling included

**Testing:**
- [ ] Tested with required arguments
- [ ] Tested with optional arguments
- [ ] Tested with special characters
- [ ] Tool restrictions validated

**Documentation:**
- [ ] Added to project README (if project command)
- [ ] Usage examples provided
- [ ] Committed to git (if project command)

**Target:** 10/10 items checked

---

## Subagent Creation Workflow

## Document Purpose

This context provides a **prescriptive workflow** for creating Claude Code subagents. Follow this process to build specialized assistants for focused sub-tasks.

**Target audience:** Developers creating specialized AI assistants
**Usage:** Follow sequentially when creating any new subagent

---

## Prerequisites

Before starting:
- [ ] Reviewed `knowledge_artifact_comparison_matrix.md` to confirm a subagent is appropriate
- [ ] Read `knowledge_subagent_specification.md` to understand subagent structure
- [ ] Identified a task requiring isolated context or specialized focus
- [ ] Determined scope (project vs user)

**Decision checkpoint:** Subagents are for ISOLATED, COMPLEX SUB-TASKS. If the main conversation can handle it, a subagent may be unnecessary.

---

## Workflow Overview

```
1. Task Scope Definition (5 min)
   └─ Define specialization and boundaries

2. System Prompt Design (15-25 min)
   └─ Write focused instructions

3. Configuration (5 min)
   └─ Set name, description, tools, model

4. File Creation (2 min)
   └─ Create subagent file

5. Integration Testing (10-15 min)
   └─ Test delegation and results

6. Documentation (3 min)
   └─ Document usage

Total: 40-55 minutes per subagent
```

---

## Step 1: Task Scope Definition

### Define Specialization

**What specific task type does this subagent handle?**

Examples:
- Code review (quality, security, best practices)
- API testing (requests, validation, OpenAPI compliance)
- Data analysis (statistics, visualization, pattern detection)
- Debugging (error analysis, root cause investigation)
- Test generation (unit tests, integration tests, coverage)

**Scope Decision Tree:**
```
Does task need separate context?
├─ NO → Use main agent, Skill, or Command
└─ YES
   ├─ Is task simple and quick?
   │  └─ NO to subagent → Consider Skill instead
   └─ Is task complex and focused?
      └─ YES → Create subagent
```

### Define Boundaries

**What does this subagent NOT do?**

**Example: Code Reviewer Subagent**
- ✅ Does: Review code changes, identify issues, suggest improvements
- ❌ Doesn't: Implement fixes (that's separate task)
- ❌ Doesn't: Write new features
- ❌ Doesn't: Deploy code

**Clear boundaries prevent scope creep**

### Identify Required Tools

**What tools does this subagent need?**

**Common patterns:**

**Read-only analysis:**
- Read, Grep, Glob

**Code review:**
- Read, Grep, Glob, Bash (for git diff)

**Debugging:**
- Read, Edit, Bash, Grep, Glob

**Data analysis:**
- Bash (for Python scripts), Read, Write

**Test generation:**
- Read, Write, Bash, Grep

---

## Step 2: System Prompt Design

### Prompt Structure

```markdown
# [Role Title]

You are a [specialization] focused on [domain].

## Responsibilities
[What this subagent does]

## Methodology
[Step-by-step approach]

## Output Format
[Expected result structure]

## Guidelines
[Key principles]

## Examples (optional)
[Concrete examples of good output]
```

### Role Definition

**Be specific:**
```markdown
# Code Review Specialist

You are a code review specialist focused on quality, security, and maintainability.
```

**Not vague:**
```markdown
# Helper

You help with code stuff.
```

### Methodology Section

**Provide step-by-step process:**

```markdown
## Review Process

### Step 1: Understand Changes
```bash
git diff main...HEAD
```
Read all changed files to understand modifications.

### Step 2: Security Analysis
Check for:
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication/authorization issues
- Sensitive data exposure
- Input validation problems

### Step 3: Quality Analysis
Evaluate:
- Code readability
- Naming conventions
- Code organization
- Error handling
- Testing coverage

### Step 4: Report Findings
Structure feedback by severity and category.
```

### Output Format

**Specify structure:**

```markdown
## Output Format

Provide review in this structure:

### Summary
[2-3 sentence overview]

### Critical Issues
- **[Issue]**: [Description] (file:line)
  - Impact: [High/Medium/Low]
  - Fix: [Recommendation]

### Security Concerns
[List]

### Quality Improvements
[List]

### Approval Status
✅ Approve / ⚠️ Approve with comments / ❌ Request changes
```

---

## Step 3: Configuration

### Choose Name

**Format:** lowercase-with-hyphens
**Max length:** 64 characters
**Requirements:** Descriptive, unique

**Good names:**
- `code-reviewer`
- `api-tester`
- `data-analyst`
- `test-generator`
- `debugger`

**Bad names:**
- `CodeReviewer` (camelCase)
- `code_reviewer` (underscore)
- `reviewer` (too vague)

### Craft Description

**Purpose:** Tells main agent when to delegate to this subagent

**Formula:**
```
[What it does]. [Specific capabilities]. Use for [scenarios].
```

**Examples:**

**Code Reviewer:**
```yaml
description: Reviews code for quality, security, and best practices. Use for pull request reviews, code audits, and security analysis of changes.
```

**API Tester:**
```yaml
description: Tests REST APIs by sending requests, validating responses, and comparing against OpenAPI specifications. Use for API development, integration testing, and debugging HTTP services.
```

**Data Analyst:**
```yaml
description: Analyzes datasets, computes statistics, creates visualizations, and identifies patterns using pandas and matplotlib. Use for data analysis, exploratory data analysis, and statistical tasks.
```

### Select Tools

**Decision tree:**
```
What does subagent do?
├─ Only reads/analyzes → Read, Grep, Glob
├─ Modifies code → Read, Edit, Bash, Grep, Glob
├─ Creates files → Read, Write, Bash
└─ Unsure → Omit tools field (inherits all)
```

### Select Model

**Decision tree:**
```
Task complexity?
├─ Simple, fast → haiku
├─ Standard → sonnet (default)
├─ Complex reasoning → opus
└─ Match main conversation → inherit
```

**Model guidelines:**
- **sonnet**: Default choice for most subagents
- **opus**: Complex analysis, critical decisions only
- **haiku**: Quick tasks, high-volume processing
- **inherit**: When consistency with main is important

---

## Step 4: File Creation

### Create Subagent File

**Project-level:**
```bash
touch .claude/agents/subagent-name.md
```

**User-level:**
```bash
touch ~/.claude/agents/subagent-name.md
```

### Write Complete File

```yaml
---
name: code-reviewer
description: Reviews code for quality, security, and best practices. Use for pull request reviews, code audits, and security analysis of changes.
tools: Read, Grep, Glob, Bash
model: sonnet
---

# Code Review Specialist

[... rest of system prompt ...]
```

---

## Step 5: Integration Testing

### Test 1: Explicit Delegation

**User request:**
```
"Use the code-reviewer subagent to analyze this file"
```

**Expected:** Main agent delegates to subagent explicitly

**Verify:**
- Subagent activates
- Subagent completes task
- Results returned to main
- Results are useful and formatted correctly

### Test 2: Automatic Delegation

**User request:**
```
"Review the changes in this pull request"
```

**Expected:** Main agent automatically delegates to code-reviewer based on description

**Verify:**
- Main agent recognizes need for code review
- Main agent chooses correct subagent
- Delegation happens without explicit mention

### Test 3: Tool Access

**During subagent execution:**

**Verify:**
- Subagent can access granted tools
- Subagent cannot access non-granted tools (if restricted)
- No "tool not allowed" errors

### Test 4: Context Isolation

**During delegation:**

**Verify:**
- Subagent doesn't see main conversation history
- Subagent focuses only on delegated task
- Main conversation doesn't get polluted with subagent's exploration

### Test 5: Result Quality

**After subagent returns:**

**Verify:**
- Results follow specified output format
- Results are actionable and specific
- Results answer the delegated task
- Main agent can integrate results effectively

---

## Step 6: Documentation

### Add to Project README

**For project subagents:**

```markdown
## Claude Subagents

### Code Reviewer
**Purpose:** Review code for quality, security, and best practices
**Usage:** Automatically used for PR reviews and code audits
**Tools:** Read, Grep, Glob, Bash
**Model:** Sonnet

**Example delegation:**
- "Review the changes in PR #456"
- "Audit the security of auth.js"
- "Use code-reviewer to analyze this file"

### API Tester
**Purpose:** Test REST APIs with validation
**Usage:** Automatically used for API testing tasks
**Tools:** Bash, Read, Write, WebFetch
**Model:** Sonnet

**Example delegation:**
- "Test the /users endpoint"
- "Validate API against OpenAPI spec"
```

---

## Complete Examples

### Example 1: Debugger Subagent

**File:** `.claude/agents/debugger.md`

```yaml
---
name: debugger
description: Analyzes errors, investigates root causes, and proposes fixes. Use for debugging issues, investigating failures, and troubleshooting problems.
tools: Read, Edit, Bash, Grep, Glob
model: sonnet
---

# Debugging Specialist

You are a debugging specialist focused on identifying and resolving issues quickly.

## Debugging Process

### 1. Reproduce the Error
- Understand failure scenario
- Identify reproduction steps
- Capture error messages and stack traces

### 2. Isolate the Cause
- Use git bisect for regressions
- Add logging/debugging statements
- Test hypotheses systematically

### 3. Analyze Root Cause
- Identify actual vs expected behavior
- Trace execution flow
- Check assumptions and edge cases

### 4. Propose Fix
- Implement fix
- Add test to prevent regression
- Verify fix resolves issue

## Output Format

### Error Summary
- **Error**: [Message]
- **Location**: [File:line]
- **Context**: [When/how it occurs]

### Root Cause
[Detailed explanation]

### Proposed Fix
```[language]
[Code fix with explanation]
```

### Testing
[Verification steps]

### Prevention
[Suggestions to prevent similar issues]

## Guidelines
- Be systematic and thorough
- Test hypotheses before declaring root cause
- Provide specific, actionable fixes
- Always suggest prevention measures
```

**Usage:**
- "Debug why the login function is failing"
- "Investigate the 500 error on /api/users"
- "Use debugger to analyze this crash"

---

### Example 2: Test Generator Subagent

**File:** `.claude/agents/test-generator.md`

```yaml
---
name: test-generator
description: Generates unit and integration tests for code. Use when creating tests, improving test coverage, or ensuring code is testable.
tools: Read, Write, Bash, Grep
model: sonnet
---

# Test Generation Specialist

You are a test generation specialist focused on creating comprehensive, maintainable tests.

## Test Generation Process

### 1. Analyze Code
- Read source code to understand functionality
- Identify testable units
- Note edge cases and error conditions

### 2. Design Test Cases
For each function/method:
- **Happy path**: Normal usage
- **Edge cases**: Boundary conditions
- **Error cases**: Invalid inputs, exceptions
- **Integration**: Dependency interactions

### 3. Generate Tests
Follow project conventions:
- Test file naming
- Test framework (Jest, Pytest, etc.)
- Assertion style
- Mocking patterns

### 4. Ensure Coverage
- Aim for >80% code coverage
- Test all public APIs
- Test critical paths thoroughly

## Test Structure

```[language]
describe('[Component] [Functionality]', () => {
  beforeEach(() => {
    [Setup]
  });

  test('should [behavior] when [condition]', () => {
    // Arrange
    [Setup test data]

    // Act
    [Execute code]

    // Assert
    [Verify outcome]
  });
});
```

## Output

Provide:
1. Test file path
2. Complete test code
3. Coverage analysis
4. Additional test suggestions

## Guidelines
- Follow AAA pattern (Arrange-Act-Assert)
- Use descriptive test names
- Test one thing per test
- Mock external dependencies
- Aim for fast, reliable tests
```

**Usage:**
- "Generate tests for auth.js"
- "Create integration tests for the API"
- "Use test-generator to add test coverage"

---

## Common Patterns

### Pattern 1: Specialized Analysis

**Subagents for focused analysis:**
- Code reviewer (quality, security)
- Performance analyzer (bottlenecks, optimization)
- Security auditor (vulnerabilities)

### Pattern 2: Content Generation

**Subagents for creating content:**
- Test generator
- Documentation writer
- API specification generator

### Pattern 3: Problem Solving

**Subagents for specific problems:**
- Debugger (error investigation)
- Optimizer (performance improvements)
- Refactorer (code restructuring)

---

## Troubleshooting

### Subagent Never Invoked

**Fix:**
- Make description more specific
- Include explicit task type keywords
- Test with explicit delegation

### Subagent Output Poor Quality

**Fix:**
- Improve system prompt clarity
- Add concrete examples
- Specify output format more precisely

### Subagent Too Slow

**Fix:**
- Use haiku model for simple tasks
- Restrict tools to essentials
- Simplify system prompt

---

## Quality Checklist

- [ ] Name is descriptive and unique
- [ ] Description clearly explains when to delegate
- [ ] Tools are appropriate for task
- [ ] Model selection is justified
- [ ] System prompt is clear and detailed
- [ ] Output format is specified
- [ ] Tested with explicit delegation
- [ ] Tested with automatic delegation
- [ ] Results are high quality
- [ ] Documented in project README (if project subagent)

**Target:** 10/10 items checked

---

## Hook Creation Workflow

## Document Purpose

This context provides a **prescriptive, security-focused workflow** for creating Claude Code hooks. Hooks execute arbitrary commands automatically and require MANDATORY security review.

**⚠️ CRITICAL: This is the HIGHEST RISK artifact type. Skip no steps.**

**Target audience:** Advanced developers implementing event-driven automation
**Usage:** Follow every step sequentially, especially security review

---

## Prerequisites

Before starting:
- [ ] Reviewed `knowledge_artifact_comparison_matrix.md` to confirm a hook is appropriate
- [ ] Read `knowledge_hook_specification.md` to understand security implications
- [ ] Identified an event-driven need (not a task that could be a Skill/Command/Subagent)
- [ ] Have security-aware peer available for review

**Decision checkpoint:** Hooks are for AUTOMATED EVENT RESPONSES. If user should trigger it, use a Command. If Claude should reason about it, use a Skill/Subagent.

---

## Workflow Overview

```
1. Need Analysis & Justification (10 min)
   └─ Why a hook? What event? What action?

2. Security Pre-Assessment (15 min) ⚠️ MANDATORY
   └─ Threat model, worst-case analysis

3. Event & Matcher Selection (5 min)
   └─ Choose event type and pattern

4. Command Design (15-25 min)
   └─ Write safe, validated command

5. Security Review (20-30 min) ⚠️ MANDATORY
   └─ Peer review with security checklist

6. Isolated Testing (15-20 min)
   └─ Test in safe environment

7. Production Deployment (10 min)
   └─ Enable with monitoring

Total: 90-115 minutes per hook (CANNOT be rushed)
```

---

## Step 1: Need Analysis & Justification

### Why a Hook?

**Justify the need:**

**Valid reasons:**
- ✅ Validate operations before execution (prevent mistakes)
- ✅ Enforce team policies automatically (consistency)
- ✅ Integrate with external systems (CI/CD, issue trackers)
- ✅ Log important events (audit trail)
- ✅ Block unsafe operations (security)

**Invalid reasons:**
- ❌ "Make Claude do something" → Use Skill/Command instead
- ❌ "Analyze or reason about code" → Use Subagent instead
- ❌ "Avoid typing a command" → Use Command instead
- ❌ "Just to try hooks" → Don't create unnecessary hooks

**Justification template:**
```
Hook purpose: [What it does]
Why not Skill/Command/Subagent: [Reasoning]
Risk if malicious input: [Worst case]
Benefit vs risk: [Justification]
```

**Example:**
```
Hook purpose: Lint JavaScript files before editing
Why not Skill/Command: Needs to run automatically before every edit, not manual
Risk if malicious input: Could run arbitrary eslint config, but limited to linting
Benefit vs risk: Prevents committing unlinted code (high value), low risk with input validation
```

### Identify Event and Action

**What event triggers this?**
- PreToolUse (before tool execution)
- PostToolUse (after tool success)
- UserPromptSubmit (when user submits prompt)
- SessionStart/SessionEnd
- Stop/SubagentStop
- Others (see specification)

**What action occurs?**
- Validation (exit 2 to block)
- Notification (log, alert)
- Integration (call external API)
- Preparation (setup environment)

---

## Step 2: Security Pre-Assessment ⚠️ MANDATORY

### Threat Modeling

**Answer these questions:**

**Q1: What is the absolute worst thing that could happen if this hook has a bug?**

Examples:
- Acceptable: "Linting fails unnecessarily"
- Concerning: "Some files aren't linted"
- UNACCEPTABLE: "Entire project deleted"

**If answer is UNACCEPTABLE, reconsider if hook is necessary.**

**Q2: Can malicious input cause unintended command execution?**

Test with:
- `'; rm -rf /; '`
- `$(whoami)`
- `../../../etc/passwd`
- `` `malicious` ``

**If YES without proper sanitization, hook MUST include input validation.**

**Q3: Does the hook access sensitive data?**

Examples:
- `.env` files
- `.git/` directory
- Credentials, API keys
- SSH keys
- User data

**If YES, hook must NOT log this data and must have strong access controls.**

**Q4: Can the hook be triggered repeatedly to cause denial of service?**

Examples:
- Expensive operations on every tool use
- Network calls that can hang
- Infinite loops

**If YES, hook must have timeout and rate limiting considerations.**

### Worst-Case Impact Analysis

**Score the hook:**

| Factor | Score (1-5) | Notes |
|--------|-------------|-------|
| Data loss risk | [1-5] | Could it delete data? |
| Credential exposure | [1-5] | Could it leak secrets? |
| System compromise | [1-5] | Could it harm the system? |
| Availability impact | [1-5] | Could it cause downtime? |

**Scoring:**
- 1-2: Low risk
- 3: Medium risk (needs careful review)
- 4-5: High risk (needs extensive validation + peer review)

**If ANY score is 4-5, this hook requires two peer reviews from security-aware developers.**

---

## Step 3: Event & Matcher Selection

### Choose Event Type

**Decision tree:**
```
When should hook run?
├─ Before tool execution? → PreToolUse
├─ After tool success? → PostToolUse
├─ On user input? → UserPromptSubmit
├─ Session lifecycle? → SessionStart/SessionEnd
└─ Agent decisions? → Stop/SubagentStop
```

**Common uses:**

**PreToolUse:**
- Validation before edit
- Security checks before execution
- Policy enforcement

**PostToolUse:**
- Run tests after code changes
- Update dependencies after package.json
- Trigger CI/CD after git push

**UserPromptSubmit:**
- Check for sensitive data in prompts
- Log user requests
- Enforce input policies

### Design Matcher Pattern

**Matcher is a regex (case-sensitive):**

**Specific (recommended):**
```json
"matcher": "Edit"
"matcher": "Edit.*\\.js$"
"matcher": "Bash.*git commit"
```

**Broad (use carefully):**
```json
"matcher": "Edit|Write"
"matcher": ".*"  // Matches everything - use VERY carefully
```

**Security principle: Be as specific as possible**

---

## Step 4: Command Design

### Command Template (Safe Pattern)

```bash
#!/bin/bash
set -euo pipefail  # Exit on error, undefined vars, pipe failures

# 1. Validate inputs
if [ -z "$REQUIRED_VAR" ]; then
    echo "Error: Required variable missing" >&2
    exit 2  # Block operation
fi

# 2. Sanitize inputs (if processing user data)
SAFE_VAR=$(echo "$USER_VAR" | sed 's/[^a-zA-Z0-9._-]//g')

# 3. Validate paths (prevent traversal)
if [[ "$FILE_PATH" != "$CLAUDE_PROJECT_DIR"* ]]; then
    echo "Error: Path outside project" >&2
    exit 2
fi

# 4. Perform operation (with quoted variables)
your_command "$SAFE_VAR" "$FILE_PATH"

# 5. Check result
if [ $? -eq 0 ]; then
    echo "Success"
    exit 0
else
    echo "Operation failed" >&2
    exit 2
fi
```

### Security Checklist During Design

- [ ] All variables quoted: `"$VAR"` not `$VAR`
- [ ] Input validation present
- [ ] Path traversal prevented (`..` filtered)
- [ ] No credential file access
- [ ] Timeout configured (< 300s typical)
- [ ] Error messages don't expose sensitive data
- [ ] No destructive operations without validation
- [ ] Uses `set -euo pipefail` for safety

---

## Step 5: Security Review ⚠️ MANDATORY

### Peer Review Requirements

**MANDATORY: Every hook must be reviewed by at least ONE other developer.**

**For high-risk hooks (score 4-5 in pre-assessment), TWO reviews required.**

### Security Review Checklist

**Reviewer must verify:**

#### Input Handling
- [ ] All variables are properly quoted
- [ ] Input validation is present and sufficient
- [ ] Path traversal is prevented
- [ ] Special characters are handled safely
- [ ] No command injection vulnerabilities

#### Credential Protection
- [ ] Hook doesn't access `.env` files
- [ ] Hook doesn't access `.git/` directory
- [ ] Hook doesn't read credential files
- [ ] Hook doesn't log sensitive data
- [ ] Error messages don't expose secrets

#### Safe Failure
- [ ] Timeout is configured and reasonable
- [ ] Errors don't expose sensitive information
- [ ] Failure mode is safe (doesn't trigger destructive fallback)
- [ ] Exit codes are correct (0=success, 2=block)

#### Minimal Permissions
- [ ] Hook has only necessary permissions
- [ ] No unnecessary file access
- [ ] No overly broad matcher patterns
- [ ] Principle of least privilege followed

#### No Destructive Operations
- [ ] No `rm -rf` without careful validation
- [ ] No disk operations (`dd`, `mkfs`)
- [ ] No system file modifications
- [ ] No fork bombs or similar

**Reviewer signature:**
```
Reviewed by: [Name]
Date: [YYYY-MM-DD]
Risk assessment: [Low/Medium/High]
Approved: [YES/NO]
Concerns: [Any concerns or notes]
```

---

## Step 6: Isolated Testing

### Test in Safe Environment

**NEVER test hooks in production first**

**Test environment options:**
1. Separate test project
2. Docker container
3. VM or sandbox
4. Dedicated test directory

### Test Protocol

**Test 1: Happy Path**
```
Trigger: [Normal operation]
Expected: Hook runs, exits 0
Result: [ ] PASS [ ] FAIL
```

**Test 2: Validation Blocking**
```
Trigger: [Invalid operation that should block]
Expected: Hook blocks with exit 2, clear error
Result: [ ] PASS [ ] FAIL
```

**Test 3: Malicious Input (Security Test)**
```
Input: '; rm -rf /'
Input: $(whoami)
Input: ../../../etc/passwd
Expected: Hook sanitizes or blocks, no execution
Result: [ ] PASS [ ] FAIL
```

**Test 4: Timeout**
```
Trigger: [Operation that exceeds timeout]
Expected: Hook killed after timeout
Result: [ ] PASS [ ] FAIL
```

**Test 5: Error Handling**
```
Trigger: [Operation that causes error]
Expected: Safe failure, clear message, no sensitive data leak
Result: [ ] PASS [ ] FAIL
```

### Debug Mode Testing

```bash
claude --debug
```

**Verify:**
- Hook executes when expected
- Hook output is correct
- Exit codes are correct
- No unexpected behavior

---

## Step 7: Production Deployment

### Gradual Rollout

**Step 1: Enable for one user**
- Add to `.claude/settings.local.json` (gitignored)
- Test in real usage
- Monitor for issues

**Step 2: Enable for team (if safe)**
- Move to `.claude/settings.json`
- Commit with comprehensive PR description
- Notify team of new hook

**Step 3: Monitor**
- Check hook logs
- Watch for errors
- Collect feedback

### Monitoring

**Add logging:**
```bash
echo "Hook executed: $(date)" >> .claude/hook-audit.log
```

**Monitor:**
- Execution frequency
- Error rate
- Performance impact
- User complaints

### Rollback Plan

**If issues occur:**
1. Remove from `settings.json` immediately
2. Notify team
3. Investigate root cause
4. Fix and re-test
5. Redeploy after verification

---

## Complete Examples

### Example 1: Lint Before Edit (Safe)

**File:** `.claude/settings.json`

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit.*\\.js$",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'set -euo pipefail; FILE=\"$FILE_PATH\"; if [[ \"$FILE\" != \"$CLAUDE_PROJECT_DIR\"* ]]; then echo \"Path validation failed\" >&2; exit 2; fi; if [[ \"$FILE\" == *.js ]]; then eslint \"$FILE\" --max-warnings 0 || { echo \"ESLint errors in $FILE\" >&2; exit 2; }; fi; exit 0'",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ Variables quoted properly
- ✅ Path validation prevents traversal
- ✅ File extension validated
- ✅ Timeout configured
- ✅ Clear error messages
- ✅ Exit codes correct

---

### Example 2: Security Audit After Dependency Changes (Medium Risk)

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write.*package\\.json|Edit.*package\\.json",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'set -euo pipefail; cd \"$CLAUDE_PROJECT_DIR\" && npm audit --audit-level=high --json > /tmp/audit-$$.json && HIGH=$(jq \".metadata.vulnerabilities.high\" /tmp/audit-$$.json) && rm /tmp/audit-$$.json && if [ \"$HIGH\" -gt 0 ]; then echo \"⚠️ $HIGH high-severity vulnerabilities detected. Run: npm audit\" >&2; exit 2; fi; exit 0'",
            "timeout": 90
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ Uses project directory safely
- ✅ Temporary file has unique name ($$)
- ✅ Cleans up temporary file
- ✅ No sensitive data logged
- ✅ Reasonable timeout for network operation

---

### Example 3: Prevent Commits to Main (Safe)

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash.*git commit",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'set -euo pipefail; BRANCH=$(git branch --show-current); if [ \"$BRANCH\" = \"main\" ] || [ \"$BRANCH\" = \"master\" ]; then echo \"❌ Direct commits to $BRANCH are not allowed. Create a feature branch.\" >&2; exit 2; fi; exit 0'",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ No user input processed
- ✅ Fast operation
- ✅ Non-destructive validation
- ✅ Clear error message

---

## Common Mistakes and How to Avoid

### Mistake 1: Unquoted Variables

**Wrong:**
```bash
rm -rf $FILE_PATH  # Command injection possible
```

**Right:**
```bash
rm -rf "$FILE_PATH"
```

### Mistake 2: No Input Validation

**Wrong:**
```bash
cat "$FILE_PATH"  # Path traversal possible
```

**Right:**
```bash
if [[ "$FILE_PATH" != "$CLAUDE_PROJECT_DIR"* ]]; then
    exit 2
fi
cat "$FILE_PATH"
```

### Mistake 3: Exposing Sensitive Data

**Wrong:**
```bash
echo "API key check failed: $API_KEY" >&2
```

**Right:**
```bash
echo "API key validation failed" >&2
```

### Mistake 4: No Timeout

**Wrong:**
```json
{"command": "npm test"}  // Could hang forever
```

**Right:**
```json
{"command": "npm test", "timeout": 120}
```

---

## Quality & Security Checklist

**Pre-deployment final check:**

**Security (MANDATORY):**
- [ ] Peer reviewed by security-aware developer
- [ ] All inputs validated
- [ ] Variables properly quoted
- [ ] No credential access
- [ ] Path traversal prevented
- [ ] Timeout configured
- [ ] Tested with malicious inputs
- [ ] Safe failure mode verified

**Functionality:**
- [ ] Tested in isolated environment
- [ ] Happy path works
- [ ] Blocking works (if applicable)
- [ ] Error messages are clear
- [ ] Performance is acceptable

**Documentation:**
- [ ] Purpose documented
- [ ] Security considerations noted
- [ ] Team notified (if project hook)
- [ ] Monitoring plan in place

**Target:** 14/14 items checked

---

## Validation and Testing

## Document Purpose

This context provides **comprehensive validation and testing procedures** for all Claude Code artifacts: Skills, Commands, Subagents, and Hooks. Use this to ensure artifacts are well-formed, function correctly, and provide value.

**Target audience:** All developers creating Claude Code artifacts
**Usage:** Apply appropriate validation sections based on artifact type

---

## Validation Philosophy

**Three layers of validation:**

1. **Syntactic Validation** - Structure and format correctness
2. **Semantic Validation** - Logical correctness and consistency
3. **Behavioral Validation** - Runtime behavior and activation testing

**Quality gate:** All three layers must pass before considering an artifact production-ready.

---

## Universal Validation (All Artifact Types)

### File System Validation

**Checklist applicable to all artifacts:**

- [ ] File exists at correct location
- [ ] Filename follows naming conventions (lowercase, hyphens)
- [ ] File permissions are correct (readable by Claude Code)
- [ ] No hidden characters or encoding issues (use UTF-8)
- [ ] File size is reasonable (< 1MB for single files)

**Command-line checks:**

```bash
# Check file exists
test -f /path/to/artifact && echo "✅ Exists" || echo "❌ Missing"

# Check encoding
file -I /path/to/artifact | grep "utf-8"

# Check size
ls -lh /path/to/artifact
```

### Markdown Syntax Validation

**For all `.md` files (Skills, Commands, Subagents):**

- [ ] Proper markdown formatting (headers, lists, code blocks)
- [ ] No unclosed code blocks
- [ ] No malformed links
- [ ] Headers follow hierarchy (don't skip levels)

**Tools:**

```bash
# Use markdownlint if available
markdownlint SKILL.md

# Or use online validators
# https://markdownlint.github.io/
```

---

## YAML Frontmatter Validation

### Common YAML Structure

**All artifacts with YAML frontmatter use this structure:**

```yaml
---
field1: value1
field2: value2
---

[Markdown content]
```

### Validation Checklist

**Syntax validation:**
- [ ] Opening `---` on line 1 (no leading whitespace)
- [ ] Closing `---` before markdown content
- [ ] No tab characters (use spaces only)
- [ ] Proper indentation (2 or 4 spaces consistently)
- [ ] Quoted values use straight quotes (`"` not `"` or `"`)
- [ ] No trailing whitespace after values

**Command-line validation:**

```bash
# Extract and validate YAML using Python
python3 <<EOF
import yaml
import sys

with open('$ARTIFACT_FILE', 'r') as f:
    content = f.read()

# Extract frontmatter
if not content.startswith('---'):
    print("❌ Missing opening ---")
    sys.exit(1)

parts = content.split('---', 2)
if len(parts) < 3:
    print("❌ Missing closing ---")
    sys.exit(1)

try:
    metadata = yaml.safe_load(parts[1])
    print(f"✅ Valid YAML: {list(metadata.keys())}")
except yaml.YAMLError as e:
    print(f"❌ YAML Error: {e}")
    sys.exit(1)
EOF
```

**Alternative: Use yq tool**

```bash
# Extract YAML frontmatter
sed -n '/^---$/,/^---$/p' SKILL.md | yq eval - || echo "❌ Invalid YAML"
```

### Field-Specific Validation

**`name` field (Skills, Subagents):**
- [ ] Present (required)
- [ ] Lowercase letters only
- [ ] Hyphens for word separation (no underscores)
- [ ] No spaces
- [ ] Max 64 characters
- [ ] Matches directory/file name

```bash
# Validate name format
NAME=$(grep "^name:" SKILL.md | cut -d: -f2 | xargs)
if [[ $NAME =~ ^[a-z0-9-]+$ ]] && [ ${#NAME} -le 64 ]; then
    echo "✅ Valid name format"
else
    echo "❌ Invalid name: $NAME"
fi
```

**`description` field (Skills, Commands, Subagents):**
- [ ] Present (required)
- [ ] Non-empty
- [ ] Max 1024 characters (Skills)
- [ ] Descriptive and specific (not vague)
- [ ] Includes trigger keywords (Skills)
- [ ] No placeholder text ("TODO", "[FILL IN]")

```bash
# Check description length
DESC=$(grep "^description:" SKILL.md | cut -d: -f2- | xargs)
LENGTH=${#DESC}
if [ $LENGTH -gt 0 ] && [ $LENGTH -le 1024 ]; then
    echo "✅ Description length OK ($LENGTH chars)"
else
    echo "❌ Description length invalid: $LENGTH chars"
fi

# Check for placeholders
if echo "$DESC" | grep -Ei "TODO|FILL|PLACEHOLDER|XXX"; then
    echo "❌ Description contains placeholders"
else
    echo "✅ No placeholders"
fi
```

**`allowed-tools` field (Skills, Commands):**
- [ ] If present, uses comma-separated format (not YAML array)
- [ ] Tool names are valid
- [ ] At least one tool specified (if field present)

```bash
# Validate allowed-tools format
TOOLS=$(grep "^allowed-tools:" SKILL.md | cut -d: -f2-)
if [ -n "$TOOLS" ]; then
    # Check if it's comma-separated (not array)
    if echo "$TOOLS" | grep -q ","; then
        echo "✅ Comma-separated format"
    else
        # Single tool is OK too
        echo "✅ Single tool specified"
    fi

    # Check for array syntax (wrong)
    if echo "$TOOLS" | grep -q "^[[:space:]]*-"; then
        echo "❌ Using array syntax (should be comma-separated)"
    fi
fi
```

**`tools` field (Subagents):**
- [ ] If present, uses comma-separated format
- [ ] Tool names are valid
- [ ] Appropriate for subagent's purpose

**`model` field (Commands, Subagents):**
- [ ] If present, is one of: `sonnet`, `opus`, `haiku`, `inherit`
- [ ] Appropriate for task complexity

```bash
# Validate model field
MODEL=$(grep "^model:" agent.md | cut -d: -f2 | xargs)
if [ -n "$MODEL" ]; then
    case "$MODEL" in
        sonnet|opus|haiku|inherit)
            echo "✅ Valid model: $MODEL"
            ;;
        *)
            echo "❌ Invalid model: $MODEL"
            ;;
    esac
fi
```

---

## Skill-Specific Validation

### Directory Structure Validation

```bash
# Check Skill directory structure
SKILL_DIR=".claude/skills/my-skill"

# Required: SKILL.md exists
test -f "$SKILL_DIR/SKILL.md" && echo "✅ SKILL.md exists" || echo "❌ Missing SKILL.md"

# Optional: Check for supporting files
if [ -d "$SKILL_DIR/templates" ]; then
    echo "ℹ️ Templates directory found"
fi

# Verify directory name matches YAML name
DIR_NAME=$(basename "$SKILL_DIR")
YAML_NAME=$(grep "^name:" "$SKILL_DIR/SKILL.md" | cut -d: -f2 | xargs)
if [ "$DIR_NAME" == "$YAML_NAME" ]; then
    echo "✅ Directory name matches YAML name"
else
    echo "❌ Mismatch: dir=$DIR_NAME, yaml=$YAML_NAME"
fi
```

### Description Quality Validation

**Automated checks:**

```bash
DESC=$(grep "^description:" SKILL.md | cut -d: -f2- | xargs)

# Check for concrete keywords
KEYWORD_COUNT=$(echo "$DESC" | grep -oE '[A-Z]{2,}|PDF|API|SQL|JSON|HTTP|CSV|YAML' | wc -l)
if [ $KEYWORD_COUNT -ge 3 ]; then
    echo "✅ Contains specific keywords ($KEYWORD_COUNT found)"
else
    echo "⚠️ Few specific keywords ($KEYWORD_COUNT), may need more"
fi

# Check for action verbs
if echo "$DESC" | grep -qE "Extract|Analyze|Test|Validate|Generate|Debug|Optimize|Convert|Process|Parse"; then
    echo "✅ Contains action verbs"
else
    echo "⚠️ No clear action verbs found"
fi

# Check for trigger scenarios
if echo "$DESC" | grep -qi "use when\|use for"; then
    echo "✅ Includes trigger scenarios"
else
    echo "⚠️ No 'use when/for' trigger scenarios"
fi
```

**Manual review criteria:**
- [ ] Description passes "tell a colleague" test (sounds natural)
- [ ] Includes 3+ file types, technologies, or domain terms
- [ ] Mentions 5+ action verbs or capabilities
- [ ] Contains "Use when/for" trigger scenarios
- [ ] No vague terms ("helps with", "various", "multiple")

### Activation Testing

**Test Suite for Skills:**

```markdown
## Skill Activation Test Protocol

**Skill:** [skill-name]
**Description:** [description text]

### Test 1: Direct Keyword Match
**User input:** "[Sentence using 3+ keywords from description]"
**Expected:** Skill activates
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 2: Contextual Trigger
**User input:** "[Natural request without explicit keywords]"
**Expected:** Skill activates
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 3: File-Based Trigger
**User input:** "[Request with relevant file attached]"
**Expected:** Skill activates
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 4: Boundary Test (Negative)
**User input:** "[Request from different domain]"
**Expected:** Skill does NOT activate
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 5: Synonym Test
**User input:** "[Same request with synonyms/paraphrasing]"
**Expected:** Skill activates
**Result:** [ ] PASS [ ] FAIL
**Notes:**

**Pass criteria:** 4/5 tests pass
**Overall result:** [ ] PASS [ ] FAIL
```

---

## Command-Specific Validation

### File Location Validation

```bash
# Check command file location
COMMAND_FILE=".claude/commands/my-command.md"

# Verify file exists
test -f "$COMMAND_FILE" && echo "✅ Command file exists" || echo "❌ Missing"

# Check filename convention
FILENAME=$(basename "$COMMAND_FILE" .md)
if [[ $FILENAME =~ ^[a-z0-9-]+$ ]]; then
    echo "✅ Valid filename format"
else
    echo "❌ Invalid filename: $FILENAME"
fi
```

### Argument Handling Validation

**Check for argument references:**

```bash
# Check if command uses arguments
if grep -q '\$ARGUMENTS\|\$1\|\$2\|\$3' command.md; then
    echo "✅ Command uses arguments"

    # Check for argument-hint metadata
    if grep -q "^argument-hint:" command.md; then
        echo "✅ argument-hint provided"
    else
        echo "⚠️ Missing argument-hint (recommended for arguments)"
    fi
else
    echo "ℹ️ No arguments used"
fi
```

**Argument pattern validation:**

- [ ] If using `$ARGUMENTS`, explain how arguments are parsed
- [ ] If using `$1, $2, $3`, document what each position means
- [ ] `argument-hint` metadata matches actual argument usage
- [ ] Example invocations show correct argument syntax

### Command Testing

**Test protocol:**

```markdown
## Command Test Protocol

**Command:** /my-command
**Arguments:** [argument pattern]

### Test 1: No Arguments
**Invocation:** /my-command
**Expected behavior:** [description]
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 2: Single Argument
**Invocation:** /my-command arg1
**Expected behavior:** [description]
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 3: Multiple Arguments
**Invocation:** /my-command arg1 arg2 arg3
**Expected behavior:** [description]
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 4: Special Characters
**Invocation:** /my-command "arg with spaces" 'arg-with-quotes'
**Expected behavior:** [description]
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 5: Edge Cases
**Invocation:** /my-command [edge case input]
**Expected behavior:** [description]
**Result:** [ ] PASS [ ] FAIL
**Notes:**

**Pass criteria:** 5/5 tests pass (or N/A for inapplicable tests)
**Overall result:** [ ] PASS [ ] FAIL
```

---

## Subagent-Specific Validation

### Configuration Validation

```bash
# Validate subagent file
AGENT_FILE=".claude/agents/my-agent.md"

# Check required fields
for FIELD in name description; do
    if grep -q "^$FIELD:" "$AGENT_FILE"; then
        echo "✅ $FIELD field present"
    else
        echo "❌ Missing required field: $FIELD"
    fi
done

# Validate tools field format (if present)
if grep -q "^tools:" "$AGENT_FILE"; then
    TOOLS=$(grep "^tools:" "$AGENT_FILE" | cut -d: -f2-)
    if echo "$TOOLS" | grep -q ","; then
        echo "✅ Tools field uses comma-separated format"
    fi
fi
```

### System Prompt Quality

**Manual review criteria:**

- [ ] System prompt is clear and specific
- [ ] Defines subagent's role and responsibilities
- [ ] Provides concrete instructions (not vague guidance)
- [ ] Includes examples if workflow is complex
- [ ] Specifies output format if relevant
- [ ] No contradictions or ambiguities

**Automated checks:**

```bash
# Check system prompt length (after YAML)
PROMPT_LENGTH=$(sed -n '/^---$/,/^---$/!p' "$AGENT_FILE" | wc -c)
if [ $PROMPT_LENGTH -ge 200 ] && [ $PROMPT_LENGTH -le 50000 ]; then
    echo "✅ System prompt length reasonable ($PROMPT_LENGTH chars)"
else
    echo "⚠️ System prompt length: $PROMPT_LENGTH chars"
fi

# Check for examples in prompt
if grep -qi "example\|for instance\|such as" "$AGENT_FILE"; then
    echo "✅ System prompt includes examples"
else
    echo "⚠️ No examples found in system prompt"
fi
```

### Delegation Testing

**Test protocol:**

```markdown
## Subagent Delegation Test Protocol

**Subagent:** my-agent
**Purpose:** [subagent purpose]

### Test 1: Direct Delegation
**Main agent prompt:** "Use my-agent to [task]"
**Expected:** Subagent activates and completes task
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 2: Implicit Delegation
**Main agent prompt:** "[Task that should trigger subagent]"
**Expected:** Main agent delegates to subagent automatically
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 3: Tool Access
**Main agent prompt:** "Have my-agent [task requiring specific tools]"
**Expected:** Subagent has access to necessary tools
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 4: Context Isolation
**Main agent context:** [complex context with lots of information]
**Subagent task:** [focused sub-task]
**Expected:** Subagent operates in clean context without main clutter
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 5: Result Integration
**Subagent task:** [complete a sub-task]
**Expected:** Subagent returns results, main agent integrates them
**Result:** [ ] PASS [ ] FAIL
**Notes:**

**Pass criteria:** 5/5 tests pass
**Overall result:** [ ] PASS [ ] FAIL
```

---

## Hook-Specific Validation

### JSON Configuration Validation

**Structure check:**

```bash
# Validate hooks JSON structure
SETTINGS_FILE=".claude/settings.json"

# Check if JSON is valid
if jq empty "$SETTINGS_FILE" 2>/dev/null; then
    echo "✅ Valid JSON"
else
    echo "❌ Invalid JSON syntax"
    exit 1
fi

# Check for hooks object
if jq -e '.hooks' "$SETTINGS_FILE" >/dev/null 2>&1; then
    echo "✅ hooks object present"
else
    echo "❌ No hooks object found"
fi

# List configured hooks
echo "Configured hook events:"
jq -r '.hooks | keys[]' "$SETTINGS_FILE"
```

**Hook configuration validation:**

```bash
# Validate individual hook configuration
EVENT="PreToolUse"

jq -r ".hooks.$EVENT[]" "$SETTINGS_FILE" | while read -r hook; do
    # Check required fields
    for FIELD in matcher hooks; do
        if echo "$hook" | jq -e ".$FIELD" >/dev/null 2>&1; then
            echo "✅ $FIELD present"
        else
            echo "❌ Missing $FIELD in hook"
        fi
    done

    # Validate hook type
    TYPE=$(echo "$hook" | jq -r '.hooks[0].type')
    case "$TYPE" in
        command|prompt)
            echo "✅ Valid hook type: $TYPE"
            ;;
        *)
            echo "❌ Invalid hook type: $TYPE"
            ;;
    esac
done
```

### Security Validation (MANDATORY)

**Critical security checklist:**

- [ ] **Input sanitization:** Hook sanitizes all input variables
- [ ] **Path validation:** No path traversal vulnerabilities (`..` filtered)
- [ ] **Command injection prevention:** Variables properly quoted
- [ ] **Credential protection:** Doesn't access `.env`, `.git/`, credential files
- [ ] **Timeout configured:** Has reasonable timeout (< 300s for most hooks)
- [ ] **Error handling:** Fails safely (doesn't expose sensitive info)
- [ ] **Minimal permissions:** Uses least privilege (only necessary tools/commands)
- [ ] **Audit logging:** Logs hook execution for security review
- [ ] **No destructive operations:** Doesn't delete files or modify critical configs
- [ ] **Reviewed by security-aware developer:** At least one other person reviewed

**Automated security scan:**

```bash
# Scan hook commands for common security issues
COMMAND=$(jq -r '.hooks.PreToolUse[].hooks[].command' "$SETTINGS_FILE")

# Check for unquoted variables
if echo "$COMMAND" | grep -E '\$[A-Z_]+[^"]'; then
    echo "⚠️ WARNING: Unquoted variable detected (command injection risk)"
fi

# Check for path traversal patterns
if echo "$COMMAND" | grep -q '\.\./'; then
    echo "⚠️ WARNING: Path traversal pattern detected"
fi

# Check for credential file access
if echo "$COMMAND" | grep -Eq '\.env|credentials|\.git/|\.ssh/'; then
    echo "⚠️ WARNING: Accessing sensitive files"
fi

# Check for dangerous commands
if echo "$COMMAND" | grep -Eq 'rm -rf|dd |mkfs|:(){'; then
    echo "🚨 CRITICAL: Destructive command detected"
    exit 1
fi
```

**Manual security review questions:**

1. **What is the worst-case scenario if this hook has a bug?**
   - Acceptable: "Linting fails unnecessarily"
   - Unacceptable: "Entire project gets deleted"

2. **Can user input cause unexpected command execution?**
   - Test with: `'; echo malicious;'`, `$(whoami)`, `../etc/passwd`

3. **Does the hook access sensitive data?**
   - If YES → Can this data leak via logs or errors?

4. **Can the hook be triggered maliciously?**
   - If YES → What rate limiting or validation prevents abuse?

5. **Has another developer reviewed this hook's security?**
   - If NO → Get a second pair of eyes BEFORE enabling

### Hook Behavior Testing

**Test protocol:**

```markdown
## Hook Testing Protocol

**Hook event:** [event type]
**Matcher:** [regex pattern]
**Command/Prompt:** [hook action]

### Test 1: Positive Match
**Trigger:** [action that should match]
**Expected:** Hook executes
**Result:** [ ] PASS [ ] FAIL
**Execution time:** [seconds]
**Notes:**

### Test 2: Negative Match
**Trigger:** [action that should NOT match]
**Expected:** Hook does not execute
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 3: Blocking Behavior (if applicable)
**Trigger:** [action that should be blocked]
**Expected:** Hook blocks with exit code 2
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 4: Timeout Handling
**Trigger:** [action that takes too long]
**Expected:** Hook times out gracefully
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 5: Error Handling
**Trigger:** [action that causes hook error]
**Expected:** Hook fails safely with clear error message
**Result:** [ ] PASS [ ] FAIL
**Notes:**

### Test 6: Security Validation
**Trigger:** [malicious input pattern]
**Expected:** Hook sanitizes or rejects input
**Result:** [ ] PASS [ ] FAIL
**Notes:**

**Pass criteria:** 6/6 tests pass
**Overall result:** [ ] PASS [ ] FAIL

**Security review:** [ ] COMPLETED BY: [name] DATE: [date]
```

---

## Quality Score Calculation

### Overall Artifact Quality

**Scoring formula:**

```
Quality = (Syntactic * 0.3) + (Semantic * 0.3) + (Behavioral * 0.4)

Where each component is 0-100:
- Syntactic: Structure, format, naming correctness
- Semantic: Logical correctness, completeness, clarity
- Behavioral: Activation reliability, performance, user experience
```

**Grade scale:**
- A (90-100): Production-ready, high quality
- B (80-89): Good, minor improvements optional
- C (70-79): Acceptable, refinement recommended
- D (60-69): Deficient, significant work required
- F (0-59): Inadequate, consider rewriting

---

## Validation Checklist Template

Use this template for any artifact:

```markdown
# Artifact Validation Report

**Artifact type:** [Skill | Command | Subagent | Hook]
**Name:** [artifact-name]
**Date:** [YYYY-MM-DD]
**Validator:** [name]

## Syntactic Validation
- [ ] File exists at correct location
- [ ] Naming conventions followed
- [ ] YAML frontmatter valid
- [ ] Required fields present
- [ ] Field formats correct
- [ ] No syntax errors

**Score:** __/10
**Notes:**

## Semantic Validation
- [ ] Description is specific and clear
- [ ] Content is complete (no TODOs)
- [ ] Examples are concrete (3-5 minimum)
- [ ] Logic is sound and consistent
- [ ] Tool access is appropriate
- [ ] No placeholders or vague language

**Score:** __/10
**Notes:**

## Behavioral Validation
- [ ] Activation tests pass (4/5 minimum)
- [ ] Argument handling works correctly (if applicable)
- [ ] Tool restrictions function as expected (if applicable)
- [ ] Performance meets targets
- [ ] Security review completed (Hooks only)
- [ ] Integration tests pass (if applicable)

**Score:** __/10
**Notes:**

## Overall Assessment

**Syntactic:** __/10 (30% weight)
**Semantic:** __/10 (30% weight)
**Behavioral:** __/10 (40% weight)

**Total Score:** __/100
**Grade:** [A | B | C | D | F]

**Production Ready:** [ ] YES [ ] NO

**Recommendations:**
1.
2.
3.

**Sign-off:** _________________ Date: _______
```

---

## Continuous Validation

### When to Re-Validate

**Triggers for re-validation:**

1. **After significant changes** - Major updates to description, methodology, or structure
2. **After activation failures** - Skill doesn't activate when expected
3. **After user feedback** - Users report issues or confusion
4. **Periodic review** - Every 3-6 months for active artifacts
5. **Claude Code updates** - New version may change behavior
6. **Before production deployment** - Final validation before team rollout

### Automated Validation Pipeline

**Recommended CI/CD integration:**

```bash
#!/bin/bash
# validate-artifacts.sh

ERRORS=0

# Validate all Skills
for SKILL in .claude/skills/*/SKILL.md; do
    echo "Validating $SKILL..."

    # YAML validation
    python3 -c "import yaml; yaml.safe_load(open('$SKILL').read().split('---')[1])" || ((ERRORS++))

    # Description check
    DESC_LEN=$(grep "^description:" "$SKILL" | cut -d: -f2- | wc -c)
    [ $DESC_LEN -gt 50 ] && [ $DESC_LEN -lt 1024 ] || ((ERRORS++))
done

# Validate all Commands
for CMD in .claude/commands/*.md; do
    echo "Validating $CMD..."

    # Check for markdown validity
    markdownlint "$CMD" || ((ERRORS++))
done

# Validate all Subagents
for AGENT in .claude/agents/*.md; do
    echo "Validating $AGENT..."

    # YAML validation
    python3 -c "import yaml; yaml.safe_load(open('$AGENT').read().split('---')[1])" || ((ERRORS++))
done

# Validate hooks
if [ -f .claude/settings.json ]; then
    echo "Validating hooks..."
    jq empty .claude/settings.json || ((ERRORS++))
fi

if [ $ERRORS -eq 0 ]; then
    echo "✅ All validations passed"
    exit 0
else
    echo "❌ $ERRORS validation errors found"
    exit 1
fi
```

**Add to `.github/workflows/validate.yml` or similar CI system.**
