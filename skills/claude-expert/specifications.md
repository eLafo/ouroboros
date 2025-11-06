# Claude Code Artifact Specifications

## Overview

This document consolidates the complete technical specifications for all four Claude Code artifact types. These artifacts enable different patterns of extensibility:

- **Skills**: Model-invoked AI capabilities that Claude automatically activates based on contextual relevance
- **Commands**: User-triggered shortcuts that expand markdown prompts with optional argument support
- **Subagents**: Specialized AI assistants for delegated tasks with isolated context windows
- **Hooks**: Event-driven scripts that execute automatically in response to Claude Code events

Each artifact type serves distinct use cases and follows specific configuration patterns, all documented comprehensively below.

---

## Skill Specification

# Claude Code Skills: Complete Technical Specification

## Document Purpose

This context provides the **authoritative technical specification** for Claude Code Skills. Use this as the reference when creating, validating, or troubleshooting Skills.

**Target audience:** Plugin developers, project maintainers creating Skills
**Usage:** Reference during Skill creation and validation processes

---

## What Are Skills?

**Skills** are model-invoked AI capabilities that Claude automatically activates based on contextual relevance. They enable Claude to perform specialized workflows by loading domain-specific instructions, methodologies, and reference materials.

**Key characteristics:**
- **Model-invoked**: Claude decides when to use them (not user-triggered)
- **Context-aware**: Loaded only when needed (progressive disclosure)
- **Extensible**: Can include supporting files (docs, templates, scripts)
- **Scoped**: Can restrict tool access for security

---

## File Structure and Organization

### Directory-Based Structure

Skills use a **directory structure** with a required `SKILL.md` file:

```
.claude/skills/skill-name/
├── SKILL.md          # Required: Main skill with YAML frontmatter
├── examples.md       # Optional: Usage examples
├── reference.md      # Optional: Reference documentation
├── templates/        # Optional: Templates or scripts
│   ├── config.json
│   └── helper.py
└── assets/           # Optional: Additional resources
    └── diagram.png
```

**Key principle:** `SKILL.md` is the entry point. Supporting files are loaded progressively when Claude needs them.

### File Naming Requirements

**Skill name (directory name):**
- Lowercase letters only
- Numbers allowed
- Hyphens for separation (no underscores)
- Maximum 64 characters
- Must be unique within scope

**Valid examples:**
- `pdf-processor`
- `api-tester-v2`
- `data-analysis-helper`

**Invalid examples:**
- `PDF_Processor` (uppercase, underscore)
- `api tester` (space)
- `dataAnalysis` (camelCase)

---

## YAML Frontmatter Schema

### Required Fields

```yaml
---
name: skill-identifier
description: Brief explanation of what this Skill does and when to use it
---
```

#### `name` (required)

**Type:** String
**Format:** Lowercase with hyphens, max 64 chars
**Purpose:** Unique identifier for the Skill

**Requirements:**
- Must match directory name
- Must be unique within scope (project/user/plugin)
- Cannot conflict with built-in Skill names

**Example:**
```yaml
name: pdf-expert
```

#### `description` (required)

**Type:** String
**Max length:** 1024 characters
**Purpose:** Tells Claude **what** the Skill does and **when** to use it

**This is the most critical field** - it directly controls activation behavior.

**Requirements:**
- Include **concrete trigger terms** users naturally mention
- Describe **capabilities** clearly
- Specify **use cases** explicitly
- Use domain-specific vocabulary

**Best practices:**
- ✅ Mention specific file types, technologies, or domains
- ✅ Include action verbs (extract, analyze, generate, validate)
- ✅ List concrete use cases
- ❌ Avoid vague language ("helps with", "useful for")
- ❌ Don't make it too broad (dilutes activation signal)

**Examples:**

**❌ Too vague:**
```yaml
description: Helps with documents and files
```

**❌ Too narrow:**
```yaml
description: Extracts tables from PDF files
```

**✅ Well-crafted:**
```yaml
description: Extract text, images, and tables from PDFs. Fill PDF forms, merge documents, convert between formats (PDF↔Word). Use when working with PDF files or document processing tasks.
```

**✅ Domain-specific:**
```yaml
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes, and comparing against OpenAPI specifications. Use for API development, integration testing, or debugging HTTP services.
```

**✅ Technology-focused:**
```yaml
description: Analyze and optimize SQL queries for PostgreSQL and MySQL. Explain execution plans, suggest indexes, identify N+1 problems, and recommend query rewrites. Use when working with database performance issues or query optimization.
```

### Optional Fields

#### `allowed-tools` (optional)

**Type:** Comma-separated list of tool names
**Purpose:** Restricts which tools Claude can use when this Skill is active
**Default:** All tools available to main conversation

**Use cases:**
- Read-only operations (restrict to Read, Grep, Glob)
- Security-sensitive workflows (allow only specific tools)
- Preventing accidental modifications

**Syntax:**
```yaml
allowed-tools: Read, Grep, Glob
```

**Available tool names:**
- `Read` - Read files
- `Write` - Create/overwrite files
- `Edit` - Modify files
- `Bash` - Execute shell commands
- `Grep` - Search file contents
- `Glob` - Find files by pattern
- `Task` - Delegate to subagents
- `WebFetch` - Fetch web content
- `WebSearch` - Search the web
- Any MCP tool names (e.g., `mcp__github__create_issue`)

**Example: Read-only Skill**
```yaml
---
name: log-analyzer
description: Analyze application logs for errors, patterns, and anomalies. Use when debugging or investigating issues from log files.
allowed-tools: Read, Grep, Bash
---
```

**Example: Isolated research Skill**
```yaml
---
name: research-assistant
description: Research technical topics, summarize documentation, and gather information from the web. Use when you need to learn about unfamiliar technologies or concepts.
allowed-tools: WebSearch, WebFetch, Read
---
```

---

## Activation Mechanism

### How Claude Decides to Use a Skill

**Step 1:** User submits a request
**Step 2:** Claude evaluates all available Skill descriptions
**Step 3:** Claude determines if any Skill is relevant based on:
- Keywords in user request
- Task context and domain
- Semantic similarity between request and description

**Step 4:** If a Skill matches, Claude invokes it by loading the Skill context

### Activation Examples

**User request:** "Can you extract the tables from this quarterly-report.pdf?"

**Skill that activates:**
```yaml
---
name: pdf-processor
description: Extract text, images, and tables from PDFs. Fill forms, merge documents, convert formats. Use when working with PDF files or document processing.
---
```

**Why it activates:** Direct mention of "extract" + "tables" + ".pdf" matches description keywords.

---

**User request:** "I'm getting a 500 error from the /api/users endpoint"

**Skill that activates:**
```yaml
---
name: api-debugger
description: Debug REST APIs, test HTTP endpoints, analyze status codes and response payloads, validate against OpenAPI specs. Use for API development, integration testing, or debugging HTTP services.
---
```

**Why it activates:** Mention of "error", "endpoint", and API context matches description.

---

**User request:** "Analyze the performance of this database query"

**Skill that activates:**
```yaml
---
name: sql-optimizer
description: Analyze and optimize SQL queries for PostgreSQL and MySQL. Explain execution plans, suggest indexes, identify N+1 problems. Use when working with database performance or query optimization.
---
```

**Why it activates:** "database query" + "performance" matches description.

---

### Activation Best Practices

#### ✅ DO: Use concrete domain vocabulary

```yaml
description: Process YAML, TOML, and JSON configuration files. Validate syntax, merge configs, extract values, convert between formats. Use when working with application configuration or settings files.
```

**Activation triggers:** "config", "YAML", "JSON", "settings", "parse config file"

#### ❌ DON'T: Use generic language

```yaml
description: Helps with configuration files
```

**Problem:** Too vague - won't reliably activate when needed

#### ✅ DO: Include file extensions and technology names

```yaml
description: Lint, format, and analyze Python code. Check PEP 8 compliance, run type checkers (mypy), execute tests (pytest), and suggest improvements. Use when working with .py files or Python projects.
```

**Activation triggers:** "Python", ".py files", "pytest", "PEP 8"

#### ❌ DON'T: Be overly narrow

```yaml
description: Runs pytest with coverage
```

**Problem:** Too specific - won't activate for broader Python tasks

---

## Storage Locations and Priority

Skills can be stored in three locations with different scopes and priorities:

### 1. Project Skills (Highest Priority)

**Location:** `.claude/skills/skill-name/`
**Scope:** Current project only
**Sharing:** Committed to git, shared with team
**Use case:** Project-specific workflows, team standards

**Example:**
```
my-project/
├── .claude/
│   └── skills/
│       ├── project-specific-helper/
│       │   └── SKILL.md
│       └── custom-validator/
│           └── SKILL.md
└── src/
```

**Priority:** If a Skill with the same name exists in both project and user locations, **project Skill takes precedence**.

### 2. User Skills (Medium Priority)

**Location:** `~/.claude/skills/skill-name/`
**Scope:** All projects for this user
**Sharing:** User-specific, not shared
**Use case:** Personal productivity tools, preferred workflows

**Example:**
```
~/.claude/
└── skills/
    ├── my-custom-helper/
    │   └── SKILL.md
    └── personal-templates/
        └── SKILL.md
```

### 3. Plugin Skills (Lowest Priority)

**Location:** Bundled within Claude Code plugins
**Scope:** Available when plugin is installed
**Sharing:** Distributed via plugin system
**Use case:** Packaged capabilities for broad distribution

**Priority order:**
1. Project Skills (`.claude/skills/`)
2. User Skills (`~/.claude/skills/`)
3. Plugin Skills (plugin-bundled)

**Conflict resolution:** Higher priority location wins if names collide.

---

## Supporting Files and Progressive Disclosure

### Multi-File Skills

Skills can include **supporting documentation** that Claude loads **only when needed**:

```
.claude/skills/api-tester/
├── SKILL.md              # Always loaded (entry point)
├── http-methods.md       # Loaded if Claude needs HTTP method reference
├── examples.md           # Loaded if Claude needs examples
├── templates/
│   ├── request.json      # Loaded if Claude needs template
│   └── auth-config.yaml
└── reference/
    └── status-codes.md   # Loaded if Claude needs status code info
```

**Key principle:** Claude starts with `SKILL.md`. If more detail is needed, Claude can read supporting files.

### Organizing Supporting Files

**Best practices:**

1. **`SKILL.md` = Core methodology**
   - Main instructions and workflow
   - High-level guidance
   - Links/references to supporting files

2. **`examples.md` = Concrete examples**
   - Real-world usage scenarios
   - Sample inputs and outputs
   - Common patterns

3. **`reference.md` = Lookup information**
   - Reference tables
   - API specifications
   - Detailed technical info

4. **`templates/` = Reusable assets**
   - Code templates
   - Configuration files
   - Scripts

### Example: PDF Processor Skill

**File structure:**
```
.claude/skills/pdf-processor/
├── SKILL.md              # Core workflow
├── examples.md           # Extraction examples
├── reference.md          # PyPDF2/pdfplumber API docs
└── templates/
    ├── extract-tables.py
    └── merge-pdfs.py
```

**SKILL.md content (excerpt):**
```markdown
---
name: pdf-processor
description: Extract text, images, and tables from PDFs...
---

# PDF Processing Workflow

## Core Capabilities

1. **Text extraction**: Use pdfplumber for accurate text extraction
2. **Table extraction**: Use tabula-py for structured tables
3. **Form filling**: Use PyPDF2 for PDF form manipulation

## Extraction Process

[Core instructions here...]

For detailed API reference, see `reference.md`.
For usage examples, see `examples.md`.
For extraction scripts, see `templates/`.
```

**Claude's behavior:**
- Starts by reading `SKILL.md`
- If user asks "show me an example", Claude reads `examples.md`
- If user asks about specific API, Claude reads `reference.md`
- If user wants to run extraction, Claude reads template from `templates/`

---

## Complete Skill Examples

### Example 1: Read-Only Log Analyzer

**Purpose:** Analyze log files without making changes

```yaml
---
name: log-analyzer
description: Analyze application logs for errors, warnings, patterns, and anomalies. Parse structured logs (JSON, syslog), identify error trends, correlate events. Use when debugging issues, investigating incidents, or monitoring log files.
allowed-tools: Read, Grep, Bash
---

# Log Analysis Skill

## Capabilities

1. **Error detection**: Find errors and exceptions in logs
2. **Pattern analysis**: Identify recurring issues
3. **Timeline reconstruction**: Correlate events across logs
4. **Structured parsing**: Handle JSON and syslog formats

## Analysis Workflow

### Step 1: Understand the Log Format

First, read a sample to determine format:
- Plain text with timestamps
- JSON lines (one JSON object per line)
- Syslog format
- Custom application format

### Step 2: Search for Errors

Use Grep to find common error patterns:
```bash
# Common error keywords
grep -i "error\|exception\|fatal\|critical" logfile.log

# HTTP errors
grep -E "HTTP/(1\.1|2) [45][0-9]{2}" access.log

# Stack traces
grep -A 10 "Exception" app.log
```

### Step 3: Analyze Patterns

Look for frequency, timing, and correlation:
- How often does the error occur?
- Is it increasing or decreasing?
- Does it correlate with specific times or events?

### Step 4: Generate Summary

Provide:
- Total errors by type
- Timeline of critical events
- Root cause hypothesis
- Recommended actions

## Examples

See `examples.md` for detailed analysis scenarios.
```

---

### Example 2: API Testing Skill with Templates

**Purpose:** Test REST APIs with structured workflows

```yaml
---
name: api-tester
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes, headers, and payloads. Compare against OpenAPI specifications, test authentication, handle rate limits. Use for API development, integration testing, or debugging HTTP services.
allowed-tools: Bash, Read, Write, WebFetch
---

# API Testing Skill

## Core Testing Workflow

### 1. Understand the API

- Read OpenAPI/Swagger spec if available
- Identify base URL, authentication method, endpoints
- Note rate limits and constraints

### 2. Prepare Test Cases

For each endpoint, test:
- ✅ Happy path (valid request → 200 OK)
- ✅ Validation errors (invalid input → 400 Bad Request)
- ✅ Authentication (missing auth → 401 Unauthorized)
- ✅ Authorization (insufficient permissions → 403 Forbidden)
- ✅ Not found (invalid resource → 404 Not Found)
- ✅ Rate limits (too many requests → 429 Too Many Requests)

### 3. Send Requests

Use curl with structured commands:

```bash
# GET request with headers
curl -X GET "https://api.example.com/users/123" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/json" \
  -w "\nHTTP Status: %{http_code}\n"

# POST request with JSON payload
curl -X POST "https://api.example.com/users" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d @templates/user-payload.json \
  -w "\nHTTP Status: %{http_code}\n"
```

### 4. Validate Responses

Check:
- Status code matches expected
- Response headers (Content-Type, Cache-Control, etc.)
- Response body structure (JSON schema)
- Response time (performance)

### 5. Compare Against Specification

If OpenAPI spec exists:
- Validate request conforms to schema
- Validate response matches defined schema
- Check required fields are present

## Templates

- `templates/auth-test.sh` - Authentication testing script
- `templates/user-payload.json` - Sample user creation payload
- `templates/openapi-validate.py` - OpenAPI validation script

## Examples

See `examples.md` for complete testing scenarios.
```

---

### Example 3: Data Analysis with Python Scripts

**Purpose:** Analyze datasets using pandas and matplotlib

```yaml
---
name: data-analyst
description: Analyze datasets (CSV, JSON, Excel), compute statistics, identify patterns, detect anomalies, create visualizations (charts, graphs, plots). Use pandas, numpy, matplotlib, seaborn. Use when working with data analysis, data science, or statistical tasks.
allowed-tools: Bash, Read, Write
---

# Data Analysis Skill

## Analysis Workflow

### 1. Load and Inspect Data

```python
import pandas as pd

# Load data
df = pd.read_csv('data.csv')

# Inspect structure
print(df.info())
print(df.describe())
print(df.head())
```

### 2. Clean Data

- Handle missing values
- Remove duplicates
- Fix data types
- Handle outliers

### 3. Analyze

- Descriptive statistics (mean, median, std dev)
- Correlation analysis
- Group-by aggregations
- Time series analysis (if applicable)

### 4. Visualize

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Distribution plot
sns.histplot(df['column_name'])
plt.savefig('distribution.png')

# Correlation heatmap
sns.heatmap(df.corr(), annot=True)
plt.savefig('correlation.png')
```

### 5. Generate Report

Summarize findings:
- Key statistics
- Notable patterns or anomalies
- Visualizations
- Recommendations

## Available Scripts

- `templates/analyze-csv.py` - Standard CSV analysis
- `templates/time-series.py` - Time series analysis
- `templates/correlation-matrix.py` - Correlation analysis

## Reference

See `reference.md` for pandas API and statistical methods reference.
```

---

## Validation and Testing

### YAML Validation

Before using a Skill, validate the YAML frontmatter:

**Common errors:**
- Missing opening or closing `---`
- Missing required `name` or `description` field
- Invalid `allowed-tools` syntax (use commas, not arrays)
- Name doesn't match directory name
- Description exceeds 1024 characters

**Validation checklist:**
- [ ] YAML frontmatter has opening `---`
- [ ] YAML frontmatter has closing `---`
- [ ] `name` field present and valid (lowercase, hyphens, max 64 chars)
- [ ] `description` field present and ≤1024 characters
- [ ] If `allowed-tools` present, uses comma-separated format
- [ ] Directory name matches `name` field
- [ ] No tab characters (use spaces for indentation)

### Activation Testing

To verify Claude will use your Skill:

**Test 1: Direct mention**
Ask Claude using keywords from your description:
```
User: "Can you analyze this logfile.txt for errors?"
Expected: log-analyzer Skill activates
```

**Test 2: Contextual usage**
Provide context that should trigger the Skill:
```
User: "I'm getting errors in my API. Here's the response: [paste JSON]"
Expected: api-debugger Skill activates
```

**Test 3: File-based triggering**
Attach a file that should trigger the Skill:
```
User: "What's in this PDF?" (attaches document.pdf)
Expected: pdf-processor Skill activates
```

**If Skill doesn't activate:**
- Make description more specific with concrete keywords
- Add technology names, file extensions, or domain terms
- Include action verbs users naturally use
- Test with different phrasing variations

### Tool Restriction Testing

If using `allowed-tools`, verify restrictions:

**Test:** Request an operation that requires a blocked tool
```yaml
allowed-tools: Read, Grep
```

**User request:** "Edit this file to fix the error"

**Expected behavior:** Claude should recognize the tool restriction and either:
- Decline the edit (cannot Edit)
- Exit the Skill to use Edit tool
- Suggest alternative approach

---

## Common Issues and Troubleshooting

### Issue 1: Skill Never Activates

**Symptoms:** Claude doesn't use the Skill even when relevant

**Diagnosis:**
- Check if description is too vague
- Verify YAML is valid (no syntax errors)
- Ensure name doesn't conflict with higher-priority Skill

**Solutions:**
1. Make description more specific with concrete keywords
2. Include file types, technologies, or domain terms
3. Add action verbs users naturally mention
4. Test with explicit requests mentioning description keywords

**Example fix:**
```yaml
# Before (too vague)
description: Helps with APIs

# After (specific)
description: Test REST APIs by sending HTTP requests, validating responses, checking status codes. Use for API testing, debugging HTTP services, or validating API endpoints.
```

---

### Issue 2: YAML Syntax Errors

**Symptoms:** Skill doesn't load, error messages about YAML

**Common errors:**
```yaml
# ❌ Missing closing ---
---
name: my-skill
description: Does something

# ❌ Using array syntax instead of comma-separated
---
name: my-skill
description: Does something
allowed-tools:
  - Read
  - Write

# ❌ Tab characters (use spaces)
---
name: my-skill
	description: Does something  # Tab before description

# ❌ Name doesn't match directory
Directory: my-custom-skill/
SKILL.md:
---
name: my-skill  # ❌ Should be my-custom-skill
```

**Solutions:**
- Always close YAML with `---`
- Use comma-separated format for `allowed-tools`
- Use spaces, never tabs
- Ensure `name` matches directory name exactly

---

### Issue 3: Skill Activates Too Broadly

**Symptoms:** Skill activates when it shouldn't, interfering with normal operation

**Diagnosis:** Description is too broad or uses generic terms

**Solution:** Narrow the description to specific use cases

**Example fix:**
```yaml
# Before (too broad)
description: Helps with files and data processing

# After (focused)
description: Process CSV and Excel files for data analysis. Compute statistics, create visualizations, identify patterns using pandas and matplotlib. Use for structured data analysis tasks.
```

---

### Issue 4: Tool Restrictions Cause Issues

**Symptoms:** Skill can't complete tasks due to missing tool access

**Diagnosis:** `allowed-tools` is too restrictive for the Skill's purpose

**Solution:** Either expand `allowed-tools` or redesign Skill scope

**Example:**
```yaml
# Problem: Skill needs to create visualizations but can't write files
---
name: data-visualizer
description: Create charts and graphs from datasets
allowed-tools: Read, Bash  # ❌ Missing Write tool

# Fix: Add Write tool
allowed-tools: Read, Bash, Write  # ✅ Can now save charts
```

---

## Best Practices Summary

### Description Crafting
1. ✅ Use concrete, domain-specific vocabulary
2. ✅ Include file types and technology names
3. ✅ Mention specific use cases
4. ✅ Use action verbs users naturally mention
5. ❌ Avoid vague language ("helps with", "useful for")
6. ❌ Don't make it too narrow or too broad

### File Organization
1. ✅ Start with `SKILL.md` as entry point
2. ✅ Use supporting files for detailed reference
3. ✅ Organize templates in `templates/` subdirectory
4. ✅ Keep `SKILL.md` focused on core methodology
5. ❌ Don't put everything in one massive file
6. ❌ Don't create unnecessary subdirectories

### Tool Access
1. ✅ Use `allowed-tools` for read-only or restricted operations
2. ✅ Test that Skill has necessary tools for its purpose
3. ✅ Consider security implications of tool access
4. ❌ Don't over-restrict if Skill needs flexibility
5. ❌ Don't grant unnecessary tools (principle of least privilege)

### Testing
1. ✅ Test activation with various phrasings
2. ✅ Verify YAML syntax before deployment
3. ✅ Test tool restrictions if using `allowed-tools`
4. ✅ Validate against `instructions_validation_and_testing.md`
5. ❌ Don't assume description will work without testing
6. ❌ Don't skip validation steps

---

## Command Specification

# Claude Code Slash Commands: Complete Technical Specification

## Document Purpose

This context provides the **authoritative technical specification for Claude Code slash commands**. Use this as the reference when creating, validating, or troubleshooting commands.

**Target audience:** Developers creating team workflows or personal productivity commands
**Usage:** Reference during command creation and validation processes

---

## What Are Slash Commands?

**Slash commands** are user-triggered shortcuts that expand markdown prompts when invoked. They provide explicit, repeatable workflows without requiring Claude to infer user intent.

**Key characteristics:**
- **User-invoked**: Explicitly triggered with `/command-name`
- **Prompt expansion**: Markdown content is inserted as if user typed it
- **Argument support**: Can accept parameters via `$ARGUMENTS` or positional variables
- **Simple**: No complex activation logic (unlike Skills)
- **Team-friendly**: Easy to share via git

---

## File Format and Structure

### Basic File Format

Commands are **Markdown files** (`.md`) with optional YAML frontmatter:

**Without frontmatter (simplest):**
```markdown
Review the current git diff and provide structured feedback on:
- Code quality
- Security concerns
- Best practices
```

**With frontmatter (recommended):**
```markdown
---
description: Review git diff with structured feedback
argument-hint: [optional: focus-area]
allowed-tools: Bash, Read
---

Review the current git diff and provide structured feedback on:
- Code quality
- Security concerns
- Best practices

If focus area is specified: $1, emphasize that aspect.
```

### File Naming

**Filename becomes the command name:**
- `review-pr.md` → `/review-pr`
- `deploy.md` → `/deploy`
- `test-suite.md` → `/test-suite`

**Naming requirements:**
- Lowercase letters
- Numbers allowed
- Hyphens for separation (no underscores)
- No spaces
- Extension: `.md`

**Valid examples:**
- `review-pr.md`
- `deploy-prod.md`
- `run-tests.md`
- `analyze-logs.md`

**Invalid examples:**
- `Review_PR.md` (uppercase, underscore)
- `deploy prod.md` (space)
- `runTests.md` (camelCase)
- `review` (missing .md extension)

---

## YAML Frontmatter (Optional)

### Available Fields

#### `description` (optional)

**Type:** String
**Purpose:** Brief explanation of what the command does
**Usage:** Shown in `/help` output for discoverability

**Example:**
```yaml
description: Review pull request with structured analysis
```

**Best practices:**
- Keep to 1-2 sentences
- Describe what the command does, not how
- Use imperative voice ("Review PR", not "This reviews PRs")

#### `argument-hint` (optional)

**Type:** String
**Purpose:** Shows expected arguments in autocomplete
**Usage:** Displayed when user types the command

**Example:**
```yaml
argument-hint: <pr-number> [focus-area]
```

**Syntax conventions:**
- `<required>` - Required argument
- `[optional]` - Optional argument
- `<arg1|arg2>` - Choice between options
- `...` - Variable number of arguments

**Examples:**
```yaml
argument-hint: <file-path>
argument-hint: <branch-name> [remote]
argument-hint: <pr-number> [high|medium|low]
argument-hint: <command> [args...]
```

#### `allowed-tools` (optional)

**Type:** Comma-separated list
**Purpose:** Restricts which tools Claude can use when executing this command
**Default:** All tools available

**Example:**
```yaml
allowed-tools: Bash, Read, Grep
```

**Common patterns:**

**Read-only:**
```yaml
allowed-tools: Read, Grep, Glob
```

**Shell execution only:**
```yaml
allowed-tools: Bash
```

**File manipulation:**
```yaml
allowed-tools: Read, Write, Edit, Bash
```

**Research:**
```yaml
allowed-tools: WebSearch, WebFetch, Read
```

#### `model` (optional)

**Type:** String
**Values:** `sonnet`, `opus`, `haiku`, `inherit`
**Purpose:** Specify which model to use for this command
**Default:** Main conversation model

**Example:**
```yaml
model: opus
```

**When to use:**
- `opus` - Complex reasoning tasks
- `sonnet` - Balanced performance (default)
- `haiku` - Quick, simple tasks
- `inherit` - Use whatever model the main conversation is using

#### `disable-model-invocation` (optional)

**Type:** Boolean
**Purpose:** Prevents Claude from executing this command via the SlashCommand tool
**Default:** `false`
**Use case:** Commands that should only be manually invoked by users

**Example:**
```yaml
disable-model-invocation: true
```

**When to use:**
- Destructive operations (deploy, delete)
- Commands that require human judgment
- Commands with security implications

---

## Storage Locations

### Project-Level Commands

**Location:** `.claude/commands/`
**Scope:** Current project only
**Sharing:** Committed to git, shared with team
**Priority:** Highest (overrides user-level if names conflict)

**Example structure:**
```
my-project/
├── .claude/
│   └── commands/
│       ├── review-pr.md
│       ├── deploy.md
│       ├── run-tests.md
│       └── workflows/
│           └── ci-check.md
└── src/
```

**Subdirectories:**
Commands can be organized in subdirectories for namespacing:

```
.claude/commands/
├── git/
│   ├── review.md    → /review (NOT /git/review)
│   └── squash.md    → /squash
└── deploy/
    ├── staging.md   → /staging
    └── prod.md      → /prod
```

**Note:** Subdirectory structure does NOT affect command name.

### User-Level Commands

**Location:** `~/.claude/commands/`
**Scope:** All projects for this user
**Sharing:** User-specific, not shared
**Priority:** Lower than project-level

**Example structure:**
```
~/.claude/
└── commands/
    ├── notes.md
    ├── summarize.md
    └── personal-helper.md
```

### Priority Resolution

If commands with the same name exist in both locations:

1. **Project-level** (`.claude/commands/`) takes precedence
2. **User-level** (`~/.claude/commands/`) is overridden

**Example:**
- Project: `.claude/commands/deploy.md`
- User: `~/.claude/commands/deploy.md`
- **Result:** `/deploy` uses project version

---

## Argument Handling

### Approach 1: $ARGUMENTS (Capture All)

**Use case:** When you want all arguments as a single string

**Syntax:** `$ARGUMENTS`

**Example command (`ask.md`):**
```markdown
---
description: Ask a question about the codebase
argument-hint: <question>
---

Answer this question about the codebase: $ARGUMENTS

Search relevant files and provide a detailed answer with references.
```

**Invocation:**
```
/ask How does authentication work in this project?
```

**Result:**
```
Answer this question about the codebase: How does authentication work in this project?

Search relevant files and provide a detailed answer with references.
```

### Approach 2: Positional Arguments ($1, $2, $3, ...)

**Use case:** When arguments appear in different parts of the prompt

**Syntax:** `$1`, `$2`, `$3`, etc.

**Example command (`review-pr.md`):**
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

Provide structured feedback focusing specifically on $2.
```

**Invocation:**
```
/review-pr 456 security
```

**Result:**
```
Fetch pull request #456 using:
```bash
gh pr view 456
```

Review the changes with emphasis on security aspects.

Provide structured feedback focusing specifically on security.
```

### Approach 3: Optional Arguments

**Pattern:** Check if argument exists before using

**Example command (`deploy.md`):**
```markdown
---
description: Deploy application to specified environment
argument-hint: <environment> [branch]
---

Deploy to $1 environment.

Branch to deploy: ${2:-main}
(Uses $2 if provided, otherwise defaults to 'main')

Steps:
1. Checkout branch: ${2:-main}
2. Build for environment: $1
3. Deploy to $1 servers
```

**Invocations:**
```
/deploy staging feature-123
# → Deploy to staging, branch: feature-123

/deploy production
# → Deploy to production, branch: main (default)
```

**Note:** `${VAR:-default}` is standard bash syntax. Claude expands this during prompt processing.

### Arguments in Code Blocks

**Arguments work inside code blocks:**

**Example command (`git-commit.md`):**
```markdown
---
argument-hint: <commit-message>
---

Create a git commit with this message:

```bash
git add .
git commit -m "$ARGUMENTS"
git push
```
```

**Invocation:**
```
/git-commit "Fix authentication bug in login flow"
```

**Result:**
```
Create a git commit with this message:

```bash
git add .
git commit -m "Fix authentication bug in login flow"
git push
```
```

### Handling Special Characters

**Quoting is automatic** - Claude handles special characters safely:

**Invocation:**
```
/ask What does the function "processData()" do?
```

**Result:** Quotes are preserved correctly in `$ARGUMENTS`

---

## Advanced Features

### File References

**Include file contents with `@` prefix:**

**Example command (`review-with-context.md`):**
```markdown
---
description: Review code with additional context
argument-hint: <file-to-review> <context-file>
---

Review the code in @$1 considering the context from @$2.

Look for:
- Inconsistencies with patterns in @$2
- Violations of principles described in @$2
```

**Invocation:**
```
/review-with-context src/auth.js docs/ARCHITECTURE.md
```

**Result:** Contents of both files are included in the prompt

### Bash Execution (Pre-command)

**Execute bash commands BEFORE the command runs:**

**Syntax:** Prefix line with `!`

**Example command (`test-changed.md`):**
```markdown
---
description: Test only files changed in current branch
allowed-tools: Bash
---

! git diff --name-only main...HEAD | grep '\.test\.js$' > /tmp/changed-tests.txt

Run these test files:

```bash
cat /tmp/changed-tests.txt | xargs npm test --
```
```

**Result:**
1. `!` command executes first
2. Output is captured
3. Rest of command can reference the result

**⚠️ Security note:** Requires `allowed-tools: Bash` in frontmatter

### Extended Thinking

**Trigger extended thinking mode** by including relevant keywords:

**Example command (`deep-analysis.md`):**
```markdown
---
description: Perform deep analysis requiring extended reasoning
---

Analyze the following in depth with careful reasoning:

$ARGUMENTS

Take your time to:
- Consider multiple perspectives
- Evaluate trade-offs
- Think through implications
- Provide thorough reasoning for conclusions
```

**Keywords that trigger extended thinking:**
- "analyze in depth"
- "careful reasoning"
- "think through"
- "evaluate trade-offs"

---

## Complete Command Examples

### Example 1: Simple Code Review

**File:** `.claude/commands/review.md`

```markdown
---
description: Review current git changes with structured feedback
allowed-tools: Bash, Read
---

Review the current git diff:

```bash
git diff
```

Provide structured feedback on:

1. **Code Quality**
   - Readability and maintainability
   - Code organization
   - Naming conventions

2. **Security**
   - Potential vulnerabilities
   - Input validation
   - Authentication/authorization

3. **Best Practices**
   - Language-specific conventions
   - Error handling
   - Testing considerations

4. **Recommendations**
   - Suggested improvements
   - Priority (high/medium/low)
```

**Usage:** `/review`

---

### Example 2: Parameterized Testing

**File:** `.claude/commands/test-file.md`

```markdown
---
description: Run tests for a specific file
argument-hint: <file-path>
allowed-tools: Bash
---

Running tests for: $1

```bash
# Determine test file from source file
TEST_FILE=$(echo "$1" | sed 's/\.js$/.test.js/' | sed 's/^src/tests/')

echo "Test file: $TEST_FILE"

if [ -f "$TEST_FILE" ]; then
    npm test -- "$TEST_FILE"
else
    echo "Test file not found: $TEST_FILE"
    exit 1
fi
```

Review test output and suggest improvements if tests fail.
```

**Usage:** `/test-file src/auth/login.js`

---

### Example 3: Multi-Argument Deployment

**File:** `.claude/commands/deploy.md`

```markdown
---
description: Deploy to specified environment with optional branch
argument-hint: <environment> [branch] [skip-tests]
allowed-tools: Bash
model: sonnet
---

🚀 Deploying to **$1** environment

**Configuration:**
- Environment: $1
- Branch: ${2:-main}
- Skip tests: ${3:-no}

**Pre-deployment checks:**

```bash
# Verify environment
if [[ ! "$1" =~ ^(staging|production)$ ]]; then
    echo "Error: Invalid environment '$1'. Use 'staging' or 'production'"
    exit 1
fi

# Checkout branch
git checkout "${2:-main}"
git pull origin "${2:-main}"

# Run tests unless skipped
if [ "${3}" != "skip" ]; then
    echo "Running test suite..."
    npm test || { echo "Tests failed! Aborting deployment."; exit 1; }
fi

# Build
echo "Building for $1..."
npm run build:$1

# Deploy
echo "Deploying to $1..."
npm run deploy:$1
```

✅ Deployment to $1 complete!

**Verification steps:**
1. Check deployment status: `npm run status:$1`
2. Monitor logs: `npm run logs:$1`
3. Verify health checks: `curl https://$1.example.com/health`
```

**Usage:**
```
/deploy staging
/deploy production main
/deploy staging feature-branch skip
```

---

### Example 4: Research and Summarize

**File:** `.claude/commands/research.md`

```markdown
---
description: Research a topic and create a summary document
argument-hint: <topic>
allowed-tools: WebSearch, WebFetch, Write
model: sonnet
---

# Research Task: $ARGUMENTS

I'll research this topic and create a comprehensive summary.

## Research Steps

1. **Web search** for recent information on: $ARGUMENTS
2. **Fetch and analyze** top relevant sources
3. **Synthesize findings** into clear summary
4. **Create markdown document** with findings

## Research Focus

- Current state of the art
- Key concepts and definitions
- Practical applications
- Recent developments (last 2 years)
- Best practices and recommendations

## Output

Creating: `research-summary-$(echo "$ARGUMENTS" | tr ' ' '-' | tr '[:upper:]' '[:lower:]').md`

After research is complete, I'll write a structured summary document.
```

**Usage:** `/research GraphQL best practices`

---

### Example 5: Team Workflow (Git Flow)

**File:** `.claude/commands/feature-start.md`

```markdown
---
description: Start a new feature branch following team conventions
argument-hint: <feature-name>
allowed-tools: Bash
---

Starting new feature: $1

```bash
# Ensure we're on main and up to date
git checkout main
git pull origin main

# Create feature branch following naming convention
BRANCH_NAME="feature/$1"
git checkout -b "$BRANCH_NAME"

echo "✅ Created and switched to branch: $BRANCH_NAME"

# Create feature tracking file
cat > .feature-info.md <<EOF
# Feature: $1

**Created:** $(date)
**Branch:** $BRANCH_NAME
**Status:** In Progress

## Description

[Describe the feature here]

## Checklist

- [ ] Implementation complete
- [ ] Tests added
- [ ] Documentation updated
- [ ] Code reviewed
- [ ] Merged to main
EOF

git add .feature-info.md
git commit -m "Start feature: $1"
git push -u origin "$BRANCH_NAME"

echo ""
echo "📝 Next steps:"
echo "1. Update .feature-info.md with feature description"
echo "2. Implement the feature"
echo "3. When ready: /feature-finish $1"
```
```

**Usage:** `/feature-start user-authentication`

---

## Command Invocation

### Basic Invocation

**Syntax:** `/command-name [arguments]`

**Examples:**
```
/review
/deploy staging
/test-file src/app.js
/research GraphQL performance optimization
```

### Autocomplete

**Typing `/` shows available commands:**
```
User types: /dep[tab]
Autocomplete suggests: /deploy <environment> [branch]
```

**Argument hints appear** if `argument-hint` is defined in frontmatter.

### Command Discovery

**List all commands:**
```
/help
```

**Output shows:**
- Command name
- Description (if provided)
- Argument hint (if provided)

**Example output:**
```
Available commands:
  /review          Review current git changes with structured feedback
  /deploy          Deploy to specified environment [argument: <env> [branch]]
  /test-file       Run tests for a specific file [argument: <file-path>]
  /research        Research a topic and create summary [argument: <topic>]
```

---

## Best Practices

### Command Design

1. **Be explicit about steps** - Don't assume Claude knows your workflow
2. **Include examples** - Show expected usage patterns
3. **Provide context** - Explain why each step matters
4. **Use tool restrictions** - Limit access with `allowed-tools` when appropriate
5. **Document arguments** - Use `argument-hint` for clarity

### Argument Handling

1. **Choose the right approach:**
   - Use `$ARGUMENTS` when all arguments are used together
   - Use `$1, $2, $3` when arguments appear in multiple places

2. **Validate arguments** - Check for required arguments in bash sections
3. **Provide defaults** - Use `${VAR:-default}` for optional arguments
4. **Quote carefully** - Ensure arguments with spaces work correctly

### Team Sharing

1. **Commit to `.claude/commands/`** - Share team workflows
2. **Document in README** - List available commands and usage
3. **Use descriptive names** - Command names should be self-explanatory
4. **Add descriptions** - Always include `description` in frontmatter
5. **Version control** - Track changes to commands over time

### Security

1. **Use `allowed-tools`** - Restrict tool access for security
2. **Validate input** - Check arguments before executing commands
3. **Avoid destructive operations** - Use `disable-model-invocation: true` for dangerous commands
4. **No hardcoded secrets** - Use environment variables for credentials

---

## Common Patterns

### Pattern 1: Workflow Chains

**Create commands that reference each other:**

**`feature-start.md`:**
```markdown
Start feature branch and set up tracking.
When done, use: /feature-finish $1
```

**`feature-finish.md`:**
```markdown
Complete feature branch and create pull request.
Merges to main after review.
```

### Pattern 2: Context Loading

**Load relevant files automatically:**

```markdown
Review @src/ARCHITECTURE.md before answering questions about: $ARGUMENTS
```

### Pattern 3: Conditional Execution

**Use bash to conditionally execute:**

```markdown
```bash
if [ "$1" == "production" ]; then
    echo "⚠️ Production deployment requires approval"
    # ... approval logic
fi
```
```

### Pattern 4: Output Formatting

**Structure output for readability:**

```markdown
## Analysis Results

**File:** $1
**Status:** [To be determined]

### Issues Found
[List issues here]

### Recommendations
[List recommendations]
```

---

## Troubleshooting

### Command Not Found

**Symptoms:** `/command` says "command not found"

**Diagnosis:**
1. Check filename: Is it `command.md` in `.claude/commands/`?
2. Restart session: Commands are loaded at session start
3. Check spelling: Command name must match filename exactly

**Fix:**
```bash
# Verify file exists
ls .claude/commands/

# Check filename matches invocation
# File: review-pr.md → Command: /review-pr
```

### Arguments Not Working

**Symptoms:** `$ARGUMENTS` or `$1` appears literally in output

**Diagnosis:**
1. Check invocation: Did you provide arguments?
2. Check syntax: Are you using correct variable format?

**Fix:**
```markdown
# Wrong
Review: ARGUMENTS

# Correct
Review: $ARGUMENTS
```

### Tool Restrictions Blocking Operations

**Symptoms:** Command fails with "tool not allowed" error

**Diagnosis:**
1. Check `allowed-tools` in frontmatter
2. Verify command needs those tools

**Fix:**
```yaml
# Add necessary tools
allowed-tools: Bash, Read, Write
```

---

## Subagent Specification

# Claude Code Subagents: Complete Technical Specification

## Document Purpose

This context provides the **authoritative technical specification for Claude Code subagents**. Use this as the reference when creating, configuring, or troubleshooting subagents.

**Target audience:** Developers creating specialized AI assistants for focused tasks
**Usage:** Reference during subagent creation and configuration

---

## What Are Subagents?

**Subagents** are specialized AI assistants that the main Claude agent can delegate tasks to. Each operates with:
- **Isolated context window** (separate from main conversation)
- **Custom system prompt** (specialized instructions)
- **Configurable tool access** (restricted or full toolset)
- **Model selection** (optimized for task complexity)

**Key characteristics:**
- **Delegated**: Main agent invokes via Task tool
- **Isolated**: Separate context prevents pollution
- **Specialized**: Focused on specific task types
- **Autonomous**: Operates independently, returns results

---

## YAML Configuration Structure

### Basic Structure

Subagents are Markdown files with YAML frontmatter:

```yaml
---
name: subagent-identifier
description: When and why to use this subagent
tools: Tool1, Tool2, Tool3
model: sonnet
---

[System prompt / instructions]
```

### Required Fields

#### `name` (required)

**Type:** String
**Format:** Lowercase with hyphens, max 64 chars
**Purpose:** Unique identifier for subagent

**Requirements:**
- Lowercase letters only
- Hyphens for word separation (no underscores)
- No spaces
- Must be unique within scope

**Examples:**
```yaml
name: code-reviewer
name: api-tester
name: data-analyst
```

#### `description` (required)

**Type:** String
**Max length:** 1024 characters
**Purpose:** Explains when main agent should delegate to this subagent

**Best practices:**
- Be specific about subagent's capabilities
- Include task types that should be delegated
- Mention domain or specialization
- Use clear, direct language

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

### Optional Fields

#### `tools` (optional)

**Type:** Comma-separated list
**Purpose:** Restricts which tools this subagent can access
**Default:** Inherits all tools from main conversation

**Syntax:**
```yaml
tools: Read, Grep, Bash
```

**Common patterns:**

**Read-only analysis:**
```yaml
tools: Read, Grep, Glob
```

**Code review:**
```yaml
tools: Read, Grep, Glob, Bash
```

**Full development:**
```yaml
tools: Read, Write, Edit, Bash, Grep, Glob
```

**Omit field to inherit all tools:**
```yaml
---
name: flexible-assistant
description: General-purpose assistant
# No tools field = inherits all tools
---
```

#### `model` (optional)

**Type:** String
**Values:** `sonnet`, `opus`, `haiku`, `inherit`
**Purpose:** Specifies which AI model processes this subagent's tasks
**Default:** Configured subagent model (typically Sonnet)

**Model characteristics:**

- **sonnet** - Balanced performance and capability (default)
  - Best for: Most tasks, code review, analysis

- **opus** - Highest capability, slower, more expensive
  - Best for: Complex reasoning, difficult problems, critical decisions

- **haiku** - Fastest, most cost-effective
  - Best for: Quick tasks, simple operations, high-volume processing

- **inherit** - Uses main conversation's model
  - Best for: Consistency with main conversation

**Examples:**
```yaml
model: sonnet    # Standard choice
model: opus      # For complex analysis
model: haiku     # For quick, simple tasks
model: inherit   # Match main conversation
```

---

## File Locations and Priority

### Storage Locations

**Project-level:** `.claude/agents/`
- Scope: Current project only
- Sharing: Committed to git, team-shared
- Priority: Highest

**User-level:** `~/.claude/agents/`
- Scope: All projects for this user
- Sharing: Personal, not shared
- Priority: Lower

**CLI-defined:** Via `--agents` flag
- Scope: Current session only
- Sharing: Not persisted
- Priority: Medium

### Priority Resolution

If subagents with same name exist in multiple locations:
1. **Project-level** (`.claude/agents/`) - Highest priority
2. **CLI-defined** (`--agents` flag) - Medium priority
3. **User-level** (`~/.claude/agents/`) - Lowest priority

**Example:**
- Project: `.claude/agents/code-reviewer.md`
- User: `~/.claude/agents/code-reviewer.md`
- **Result:** Project version is used

---

## System Prompt Design

### Purpose of System Prompt

The system prompt (markdown content after YAML) provides:
- **Role definition**: What this subagent is
- **Responsibilities**: What tasks it handles
- **Methodology**: How it approaches tasks
- **Output format**: How it structures results
- **Examples**: Concrete usage patterns (optional but recommended)

### Effective System Prompts

**Structure:**
```markdown
---
[YAML frontmatter]
---

# Role Definition
You are a [specialized role] focused on [domain].

# Responsibilities
When delegated a task, you will:
1. [Primary responsibility]
2. [Secondary responsibility]
3. [Additional responsibilities]

# Methodology
Follow this approach:
[Step-by-step process]

# Output Format
Provide results in this structure:
[Expected output format]

# Guidelines
- [Key guideline 1]
- [Key guideline 2]
- [Key guideline 3]
```

### Example: Code Reviewer

```yaml
---
name: code-reviewer
description: Reviews code for quality, security, and best practices. Use for pull request reviews and code audits.
tools: Read, Grep, Glob, Bash
model: sonnet
---

# Code Review Specialist

You are a code review specialist focused on quality, security, and maintainability.

## Responsibilities

When reviewing code:
1. Analyze changes for quality and maintainability
2. Identify security vulnerabilities
3. Check compliance with best practices
4. Provide actionable feedback

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
- Code readability and maintainability
- Naming conventions
- Code organization
- Error handling
- Testing coverage

### Step 4: Best Practices
Verify:
- Language-specific conventions followed
- Design patterns used appropriately
- Documentation adequate
- No code smells or anti-patterns

## Output Format

Provide review in this structure:

### Summary
[2-3 sentence overview]

### Critical Issues (if any)
- **[Issue]**: [Description] (file:line)
  - Impact: [High/Medium/Low]
  - Fix: [Specific recommendation]

### Security Concerns
[List with specifics]

### Quality Improvements
[List with specifics]

### Best Practice Suggestions
[List with specifics]

### Approval Status
- ✅ Approve (no blocking issues)
- ⚠️ Approve with comments (minor improvements suggested)
- ❌ Request changes (blocking issues must be fixed)
```

---

## Delegation Patterns

### Automatic Delegation

Main agent decides when to delegate based on subagent `description`:

**Example flow:**
1. User: "Review the changes in this PR"
2. Main agent reads subagent descriptions
3. Main agent finds `code-reviewer` matches the task
4. Main agent delegates via Task tool

**No explicit invocation needed**

### Explicit Delegation

User can explicitly request a subagent:

**Example:**
```
User: "Use the code-reviewer subagent to analyze this file"
```

Main agent directly invokes specified subagent.

### Task Tool Usage

**Main agent invokes subagents using Task tool:**

```python
Task(
    subagent_type="code-reviewer",
    prompt="Review the changes in PR #456 focusing on security",
    description="Code review for PR 456"
)
```

**Parameters:**
- `subagent_type`: Name of subagent (matches `name` field)
- `prompt`: Task instructions for subagent
- `description`: Brief description (for logging/tracking)

---

## Context Isolation

### Why Isolation Matters

**Problem without isolation:**
- Large main context consumed by sub-tasks
- Subagent sees irrelevant conversation history
- Main context fills up quickly

**Solution with subagents:**
- Subagent gets clean, focused context
- Only sees: system prompt + delegated task
- Results returned to main without polluting main context

**Example:**

**Main conversation:**
```
User: Help me refactor this codebase [large codebase context]
Main: I'll analyze the structure first...
[... lots of exploration ...]
Main: Now I'll delegate code review to the code-reviewer subagent
```

**Subagent sees:**
```
System: [code-reviewer system prompt]
Task: Review these specific changes [just the relevant changes]
```

**Subagent doesn't see:**
- Main conversation history
- Previous exploration
- Unrelated context

### Context Window Management

**Main agent context:**
- Full conversation history
- User requests
- Previous tool uses
- Subagent results (summaries)

**Subagent context:**
- System prompt (from subagent definition)
- Delegated task
- Tool outputs from subagent's operations
- No main conversation history

---

## Built-in Subagents

### Plan Subagent

**Automatically used in plan mode**

**Description:** Explores codebase with Read, Glob, Grep, and Bash to gather context before presenting implementation strategies.

**Tools:** Read, Glob, Grep, Bash
**Model:** Typically Sonnet

**When it activates:**
- User asks for implementation plan
- Claude enters plan mode
- Plan subagent searches codebase
- Returns findings to main agent
- Main agent synthesizes plan

---

## Complete Subagent Examples

### Example 1: Debugger

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
- Understand the failure scenario
- Identify steps to reproduce
- Capture error messages and stack traces

### 2. Isolate the Cause
- Use git bisect if regression
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
- **Error**: [Error message]
- **Location**: [File:line]
- **Context**: [When/how it occurs]

### Root Cause
[Detailed explanation of why error occurs]

### Proposed Fix
```[language]
[Code fix with explanation]
```

### Testing
[How to verify fix works]

### Prevention
[Suggestions to prevent similar issues]
```

---

### Example 2: Test Generator

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
- Identify testable units (functions, methods, classes)
- Note edge cases and error conditions

### 2. Design Test Cases
For each function/method, create tests for:
- **Happy path**: Normal, expected usage
- **Edge cases**: Boundary conditions
- **Error cases**: Invalid inputs, exceptions
- **Integration**: Interaction with dependencies

### 3. Generate Tests
Follow project conventions for:
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
describe/test('[Component] [Functionality]', () => {
  // Setup
  beforeEach(() => {
    [Common setup]
  });

  test('should [expected behavior] when [condition]', () => {
    // Arrange
    [Setup test data]

    // Act
    [Execute code under test]

    // Assert
    [Verify expected outcome]
  });
});
```

## Output

Provide:
1. Test file path
2. Complete test code
3. Coverage analysis
4. Suggestions for additional tests
```

---

### Example 3: Data Analyst

**File:** `.claude/agents/data-analyst.md`

```yaml
---
name: data-analyst
description: Analyzes datasets, computes statistics, creates visualizations, and identifies patterns using pandas and matplotlib. Use for data analysis, exploratory data analysis, and statistical tasks.
tools: Bash, Read, Write
model: sonnet
---

# Data Analysis Specialist

You are a data analysis specialist using Python, pandas, numpy, and matplotlib.

## Analysis Workflow

### 1. Load and Inspect
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Load data
df = pd.read_csv('data.csv')

# Inspect
print(df.info())
print(df.describe())
print(df.head())
```

### 2. Clean Data
- Handle missing values (impute or drop)
- Remove duplicates
- Fix data types
- Handle outliers

### 3. Analyze
- Descriptive statistics
- Correlation analysis
- Group-by aggregations
- Time series analysis (if applicable)

### 4. Visualize
```python
# Distribution
df['column'].hist(bins=30)
plt.savefig('distribution.png')

# Correlation
import seaborn as sns
sns.heatmap(df.corr(), annot=True)
plt.savefig('correlation.png')
```

### 5. Report Findings
- Key statistics
- Notable patterns
- Anomalies or outliers
- Recommendations

## Output Format

### Data Overview
- Rows: [N]
- Columns: [M]
- Missing values: [%]

### Key Statistics
[Table of statistics]

### Findings
1. [Finding 1 with supporting data]
2. [Finding 2 with supporting data]

### Visualizations
- [visualization1.png]: [Description]
- [visualization2.png]: [Description]

### Recommendations
[Actionable recommendations based on analysis]
```

---

## Resumable Subagents

### Continuing Previous Work

**Subagents can be resumed using `agentId`:**

```
User: "Resume subagent abc123 and analyze the authentication logic"
```

Main agent passes `agentId` to Task tool to continue previous subagent's context.

**Use case:** Multi-step tasks where subagent needs additional instructions

---

## Best Practices

### Subagent Design

1. **Single responsibility**: Each subagent should have one clear focus
2. **Clear boundaries**: Define exactly what tasks this subagent handles
3. **Appropriate tools**: Grant only necessary tools, not everything
4. **Detailed prompts**: Provide comprehensive system prompts
5. **Examples in prompt**: Include examples of good output

### Model Selection

1. **Default to Sonnet**: Good balance for most tasks
2. **Use Opus sparingly**: Only for genuinely complex reasoning
3. **Use Haiku for speed**: Simple, quick tasks benefit from Haiku
4. **Consider cost**: Opus is more expensive, use judiciously

### Tool Access

1. **Principle of least privilege**: Grant minimum necessary tools
2. **Read-only when possible**: Analysis tasks often don't need Write/Edit
3. **Validate tool need**: Does subagent actually use granted tools?
4. **Omit tools field for flexibility**: Inherit all tools if unsure

### Description Crafting

1. **Be specific**: Vague descriptions lead to incorrect delegation
2. **Include task types**: List scenarios that should trigger delegation
3. **Mention domain**: Clarify specialization area
4. **Keep concise**: Don't exceed 1024 characters

---

## Troubleshooting

### Subagent Never Invoked

**Symptoms:** Main agent doesn't delegate to subagent

**Diagnosis:**
1. Check description: Is it clear when to use this subagent?
2. Verify name: Does name match usage in Task tool?
3. Check location: Is file in `.claude/agents/`?

**Fix:**
- Make description more specific
- Include explicit task type keywords
- Test with explicit delegation request

### Subagent Lacks Necessary Tools

**Symptoms:** Subagent can't complete task due to missing tools

**Diagnosis:**
Check `tools` field - are necessary tools granted?

**Fix:**
```yaml
# Add missing tools
tools: Read, Write, Edit, Bash
```

**Or remove `tools` field to inherit all tools**

### Subagent Too Slow

**Symptoms:** Subagent takes too long

**Diagnosis:**
Check `model` field - using Opus when Sonnet would work?

**Fix:**
```yaml
model: sonnet  # Faster than Opus
# or
model: haiku   # Fastest for simple tasks
```

---

## Hook Specification

# Claude Code Hooks: Complete Technical Specification with Security Emphasis

## Document Purpose

This context provides the **authoritative technical specification for Claude Code Hooks** with **mandatory security guidance**. Hooks execute arbitrary shell commands automatically and require careful security review.

**⚠️ CRITICAL: Hooks are the highest-risk artifact type. Security review is MANDATORY before deployment.**

**Target audience:** Advanced developers implementing event-driven automation
**Usage:** Reference during Hook creation with strict adherence to security guidelines

---

## What Are Hooks?

**Hooks** are event-driven scripts that execute automatically in response to Claude Code events. They enable validation, policy enforcement, and integration with external systems.

**Key characteristics:**
- **Event-driven**: Triggered by specific Claude Code events (tool use, prompt submit, etc.)
- **Automated**: Execute without user intervention
- **Powerful**: Can block operations, modify behavior, or integrate with external tools
- **High-risk**: Execute arbitrary commands with potential for damage

**⚠️ Security warning:** "Hooks execute arbitrary shell commands automatically. You are solely responsible for command safety and consequences, including potential data loss or system damage."

---

## Configuration Format and Storage

###  JSON Configuration Structure

Hooks are defined in **JSON configuration files**:

- `.claude/settings.json` (project-level, committed to git)
- `.claude/settings.local.json` (project-level, gitignored for personal hooks)
- `~/.claude/settings.json` (user-level, all projects)

**Basic structure:**

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolOrPatternToMatch",
        "hooks": [
          {
            "type": "command",
            "command": "bash-command-to-execute",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

### Configuration Fields

#### `EventName` (required)

**Type:** String (top-level key in `hooks` object)
**Purpose:** The Claude Code event that triggers this hook
**Values:** See "Hook Event Types" section below

**Example:**
```json
{
  "hooks": {
    "PreToolUse": [...],
    "PostToolUse": [...],
    "UserPromptSubmit": [...]
  }
}
```

#### `matcher` (required)

**Type:** String (regex pattern)
**Purpose:** Pattern to match against tool names or event data
**Format:** Regular expression (case-sensitive)
**Special value:** `".*"` matches all

**Examples:**

```json
// Match specific tool
"matcher": "Edit"

// Match multiple tools with regex
"matcher": "Edit|Write|Delete"

// Match tool with arguments
"matcher": "Bash.*git commit"

// Match file patterns
"matcher": "Write.*\\.js$"

// Match all (use carefully)
"matcher": ".*"
```

**⚠️ Security note:** Broad matchers (`".*"`) increase attack surface. Be as specific as possible.

#### `hooks` array (required)

**Type:** Array of hook objects
**Purpose:** List of hooks to execute when matcher matches
**Execution:** All hooks run in parallel

**Hook object fields:**

##### `type` (required)

**Type:** String
**Values:** `"command"` or `"prompt"`
**Purpose:** Specifies execution mode

- `"command"`: Execute bash command
- `"prompt"`: Send to LLM for evaluation (available for `Stop` and `SubagentStop` events only)

##### `command` (required for type="command")

**Type:** String
**Purpose:** Bash command to execute
**Environment:** Runs in current working directory with Claude Code's environment variables

**Available variables:**
- `$CLAUDE_PROJECT_DIR` - Project root directory
- `$CLAUDE_CODE_REMOTE` - Execution context (local or remote)
- Event-specific variables (see "Input/Output Protocol" section)

**Example:**
```json
{
  "type": "command",
  "command": "eslint \"$FILE_PATH\" --fix",
  "timeout": 30
}
```

**⚠️ Security critical:** ALWAYS quote variables: `"$VAR"` not `$VAR`

##### `prompt` (required for type="prompt")

**Type:** String
**Purpose:** Prompt sent to LLM for evaluation
**Availability:** Only for `Stop` and `SubagentStop` events
**Variables:** Can use `$ARGUMENTS` to reference event data

**Example:**
```json
{
  "type": "prompt",
  "prompt": "Evaluate if Claude should stop: $ARGUMENTS. Check if all tasks are complete.",
  "timeout": 30
}
```

**Response:** LLM returns JSON with `decision` ("approve" or "block") and `reason`.

##### `timeout` (optional)

**Type:** Number (seconds)
**Default:** 60
**Maximum:** 600 (10 minutes)
**Purpose:** Maximum execution time before hook is killed

**Recommendations:**
- Validation hooks: 10-30 seconds
- Build/test hooks: 120-300 seconds
- Quick checks: 5-10 seconds

**Example:**
```json
{
  "type": "command",
  "command": "npm test",
  "timeout": 120
}
```

---

## Hook Event Types

### Tool-Related Events

#### PreToolUse

**When:** Before tool execution (can block or modify)
**Use cases:**
- Validate tool arguments before execution
- Block unsafe operations
- Lint files before editing
- Check permissions

**Event data:**
- `tool_name`: Name of the tool being invoked
- `tool_input`: Tool parameters/arguments
- `cwd`: Current working directory

**Example:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit",
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

**⚠️ Security consideration:** PreToolUse can block operations entirely. Ensure failure modes are safe.

#### PostToolUse

**When:** After successful tool execution
**Use cases:**
- Run tests after code changes
- Update documentation
- Trigger CI/CD pipelines
- Log successful operations

**Event data:**
- `tool_name`: Name of the tool that was invoked
- `tool_input`: Tool parameters used
- `tool_response`: Tool output
- `cwd`: Current working directory

**Example:**
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

**Note:** Only fires on success. If tool fails, PostToolUse does not trigger.

### User Interaction Events

#### UserPromptSubmit

**When:** User submits a prompt (before processing)
**Use cases:**
- Validate prompts for sensitive data (API keys, passwords)
- Enforce input policies
- Log user requests
- Block malicious or inappropriate requests

**Event data:**
- `user_prompt`: The submitted prompt text
- `session_id`: Current session identifier

**Example:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if the user prompt contains sensitive data like API keys, passwords, or secrets. Block if found. Prompt: $ARGUMENTS",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**⚠️ Security consideration:** Can inspect all user input. Ensure hook doesn't log sensitive data itself.

#### Notification

**When:** Claude requests permission or becomes idle
**Use cases:**
- Custom permission handling
- Idle state monitoring
- Usage tracking

**Event data:**
- `notification_type`: Type of notification
- `notification_data`: Notification-specific data

**Example:**
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_request",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"Permission requested: $ARGUMENTS\" >> permissions.log",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

### Session Management Events

#### SessionStart

**When:** Session initialization
**Use cases:**
- Load project context
- Set environment variables
- Initialize external integrations
- Log session start

**Event data:**
- `session_id`: Unique session identifier
- `cwd`: Working directory
- `timestamp`: Session start time

**Example:**
```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"Session started: $(date)\" >> .claude/session.log",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

**Best practice:** Keep SessionStart hooks fast (< 5 seconds) to avoid delaying startup.

#### SessionEnd

**When:** Session termination
**Use cases:**
- Cleanup temporary files
- Save session state
- Log session end
- Trigger post-session processing

**Event data:**
- `session_id`: Session identifier
- `duration`: Session duration
- `timestamp`: End time

**Example:**
```json
{
  "hooks": {
    "SessionEnd": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "rm -f /tmp/claude-temp-* && echo \"Cleaned temp files\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**⚠️ Security note:** SessionEnd hooks run during cleanup. Ensure they don't prevent proper shutdown.

#### PreCompact

**When:** Before context compaction (manual or automatic)
**Use cases:**
- Save context state before compaction
- Log compaction events
- Prepare for context reduction

**Event data:**
- `session_id`: Current session
- `compact_reason`: Why compaction is occurring (manual/automatic)
- `context_size`: Current context size

**Example:**
```json
{
  "hooks": {
    "PreCompact": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"Context compaction at $(date)\" >> .claude/compact.log",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

### Decision Events

#### Stop

**When:** Main agent finishes and wants to stop
**Use cases:**
- Force continuation if tasks incomplete
- Validate work completeness
- Ensure quality gates pass before stopping

**Event data:**
- `session_id`: Current session
- `stop_reason`: Why agent wants to stop

**Special:** Can use `type: "prompt"` for LLM evaluation

**Example:**
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if all tasks are complete. Review session transcript. If any tasks are incomplete or quality issues exist, return decision: block. Otherwise return decision: approve. Session: $ARGUMENTS",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Use case:** Prevent Claude from stopping prematurely

#### SubagentStop

**When:** Subagent completes and wants to return
**Use cases:**
- Validate subagent work quality
- Ensure subagent completed assigned task
- Force subagent to continue if work incomplete

**Event data:**
- `subagent_type`: Type of subagent
- `task`: Task assigned to subagent
- `result`: Subagent's result

**Example:**
```json
{
  "hooks": {
    "SubagentStop": [
      {
        "matcher": "code-reviewer",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Verify code-reviewer subagent found at least 3 issues or explicitly stated code is clean. If review seems superficial, block and ask for deeper analysis. Subagent result: $ARGUMENTS",
            "timeout": 20
          }
        ]
      }
    ]
  }
}
```

---

## Input/Output Protocol

### Hook Input (via stdin)

Hooks receive JSON on stdin with:

**Common fields (all events):**
- `session_id`: Unique session identifier
- `transcript_path`: Path to conversation history
- `cwd`: Current working directory
- `permission_mode`: Permission enforcement level
- `hook_event_name`: The triggering event name

**Event-specific fields:**

**PreToolUse / PostToolUse:**
- `tool_name`: Tool being used
- `tool_input`: Tool parameters (JSON object)
- `tool_response`: Tool output (PostToolUse only)

**UserPromptSubmit:**
- `user_prompt`: Submitted prompt text

**Stop / SubagentStop:**
- `stop_reason`: Why stopping
- `task`: Task description (SubagentStop)
- `result`: Result data (SubagentStop)

**Example input:**
```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "cwd": "/Users/dev/my-project",
  "permission_mode": "ask",
  "hook_event_name": "PreToolUse",
  "tool_name": "Edit",
  "tool_input": {
    "file_path": "/Users/dev/my-project/src/app.js",
    "old_string": "const x = 1;",
    "new_string": "const x = 2;"
  }
}
```

### Hook Output

Hooks respond via:

#### Exit Codes

- **Exit 0**: Success, approve operation
  - stdout shown in transcript mode
  - Operation proceeds

- **Exit 2**: Blocking error
  - stderr fed to Claude for processing
  - Operation is blocked
  - Claude sees error message and can react

- **Any other exit code**: Non-blocking error
  - Error logged but operation proceeds

**Example:**
```bash
#!/bin/bash
# Validation hook

if [ "$VALIDATION" == "fail" ]; then
    echo "Validation failed: $ERROR_MESSAGE" >&2
    exit 2  # Block operation
fi

echo "Validation passed"
exit 0  # Allow operation
```

#### JSON Response (Advanced)

For fine-grained control, output JSON to stdout:

```json
{
  "decision": "approve|block|deny|ask",
  "reason": "Explanation for decision",
  "continue": true,
  "stopReason": "Message when stopping",
  "systemMessage": "Optional warning to show user",
  "suppressOutput": false,
  "updatedInput": {}
}
```

**Fields:**

- `decision`: Control operation flow
  - `"approve"`: Allow operation
  - `"block"`: Block with reason (stderr to Claude)
  - `"deny"`: Silent denial
  - `"ask"`: Request user permission

- `reason`: Explanation shown to Claude or user

- `continue`: Force agent continuation (Stop/SubagentStop only)

- `stopReason`: Message when stopping

- `systemMessage`: Warning message shown to user

- `suppressOutput`: Hide hook output from transcript

- `updatedInput`: Modified tool input (PreToolUse only)

**Example:**
```bash
#!/bin/bash
# Security hook with JSON response

if echo "$FILE_PATH" | grep -q "\.env"; then
    cat <<EOF
{
  "decision": "block",
  "reason": "Blocked attempt to edit .env file. Use settings management instead.",
  "systemMessage": "⚠️ Direct .env editing is disabled for security. Use 'claude config set' instead."
}
EOF
    exit 2
fi

echo '{"decision": "approve"}'
exit 0
```

---

## Security Model and Requirements

### Security Philosophy

**Principle: Hooks are dangerous by design.**

Hooks execute arbitrary commands automatically without user review. One misconfigured hook can:
- Delete entire projects
- Expose sensitive credentials
- Execute malicious code
- Compromise system security

**MANDATORY: Every hook MUST undergo security review before deployment.**

### Security Checklist (MANDATORY)

Before enabling any hook, verify:

#### Input Sanitization
- [ ] **All variables are quoted**: `"$VAR"` not `$VAR`
- [ ] **Path traversal prevented**: Filter `..` in paths
- [ ] **Special characters escaped**: Handle spaces, quotes, semicolons
- [ ] **Command injection prevented**: No unvalidated string interpolation
- [ ] **Regex validated**: Matcher patterns don't cause ReDoS

**Example (WRONG):**
```bash
# ❌ DANGEROUS: Unquoted variable allows command injection
rm -rf $FILE_PATH
# Input: "; rm -rf /" executes as: rm -rf ; rm -rf /
```

**Example (CORRECT):**
```bash
# ✅ SAFE: Quoted variable
rm -rf "$FILE_PATH"
# Input: "; rm -rf /" treated as filename, safely fails
```

#### Credential Protection
- [ ] **No .env access**: Hook doesn't read `.env` files
- [ ] **No .git/ access**: Hook doesn't access `.git/` directory
- [ ] **No credential files**: Hook doesn't read `credentials.json`, `secrets.yaml`, etc.
- [ ] **No SSH keys**: Hook doesn't access `.ssh/` directory
- [ ] **Logging is safe**: Hook logs don't expose sensitive data

**Example (WRONG):**
```bash
# ❌ DANGEROUS: Exposes credentials in logs
echo "API Key: $API_KEY" >> hook.log
```

**Example (CORRECT):**
```bash
# ✅ SAFE: Validates without exposing
if [ -z "$API_KEY" ]; then
    echo "Error: API_KEY not set" >&2
    exit 2
fi
```

#### Minimal Permissions
- [ ] **Least privilege**: Hook has only necessary permissions
- [ ] **Read-only when possible**: Validation hooks don't modify files
- [ ] **Specific paths**: Hook operates only on necessary directories
- [ ] **Tool restrictions**: Hook doesn't access unrelated tools

**Example (WRONG):**
```bash
# ❌ DANGEROUS: Too broad, could delete anything
rm -rf *
```

**Example (CORRECT):**
```bash
# ✅ SAFE: Specific, limited scope
rm -f "$CLAUDE_PROJECT_DIR/tmp/cache/*.tmp"
```

#### Safe Failure
- [ ] **Fails safely**: Errors don't expose sensitive info
- [ ] **Timeout configured**: Reasonable timeout (< 300s typical)
- [ ] **No destructive fallback**: Failure doesn't trigger dangerous cleanup
- [ ] **Error messages are safe**: No sensitive data in stderr

#### No Destructive Operations
- [ ] **No `rm -rf`**: Never recursively delete without careful validation
- [ ] **No `dd`**: No disk operations
- [ ] **No `mkfs`**: No filesystem formatting
- [ ] **No fork bombs**: No `:(){ :|:& };:`
- [ ] **No system modifications**: No `/etc/`, `/usr/`, `/sys/` access

**Example (WRONG):**
```bash
# ❌ CATASTROPHIC: Could delete entire system
rm -rf /
```

**Example (CORRECT):**
```bash
# ✅ SAFE: Limited, validated cleanup
if [ -d "$CLAUDE_PROJECT_DIR/.cache" ]; then
    rm -rf "$CLAUDE_PROJECT_DIR/.cache"
fi
```

#### Peer Review
- [ ] **Second review**: At least one other developer reviewed
- [ ] **Security-aware reviewer**: Reviewer understands security implications
- [ ] **Test in isolation**: Tested in safe environment first
- [ ] **Documented**: Security considerations documented

### Common Vulnerabilities

#### Command Injection

**Vulnerable:**
```bash
# User input: "; cat /etc/passwd"
echo "Processing: $USER_INPUT"  # Executes: echo "Processing: "; cat /etc/passwd"
```

**Fixed:**
```bash
# Proper quoting prevents injection
echo "Processing: \"$USER_INPUT\""
```

#### Path Traversal

**Vulnerable:**
```bash
# User input: "../../../etc/passwd"
cat "$FILE_PATH"  # Reads: cat ../../../etc/passwd
```

**Fixed:**
```bash
# Validate path is within project
if [[ "$FILE_PATH" != "$CLAUDE_PROJECT_DIR"* ]]; then
    echo "Error: Path outside project" >&2
    exit 2
fi
```

#### ReDoS (Regex Denial of Service)

**Vulnerable:**
```json
{
  "matcher": "(a+)+"
  // Causes exponential backtracking on input like "aaaaaaaaaaaaaaX"
}
```

**Fixed:**
```json
{
  "matcher": "a+"
  // Simple regex, O(n) time
}
```

#### Information Leakage

**Vulnerable:**
```bash
# Error exposes sensitive data
echo "Database password check failed for: $DB_PASSWORD" >&2
```

**Fixed:**
```bash
# Error message is safe
echo "Database authentication failed" >&2
```

---

## Complete Hook Examples

### Example 1: Lint Before Edit (PreToolUse)

**Purpose:** Run ESLint before editing JavaScript files

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'FILE=\"$FILE_PATH\"; if [[ \"$FILE\" == *.js ]]; then eslint \"$FILE\" --max-warnings 0 || { echo \"Lint errors in $FILE\" >&2; exit 2; }; fi'",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ Variable is quoted: `"$FILE_PATH"` and `"$FILE"`
- ✅ File extension validated: Only `.js` files
- ✅ Timeout configured: 30 seconds
- ✅ Safe failure: Exit 2 blocks edit if lint fails
- ✅ No destructive operations

---

### Example 2: Security Scan After Dependency Changes (PostToolUse)

**Purpose:** Run `npm audit` after `package.json` changes

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write.*package\\.json|Edit.*package\\.json",
        "hooks": [
          {
            "type": "command",
            "command": "cd \"$CLAUDE_PROJECT_DIR\" && npm audit --audit-level=high --json > audit-report.json && if jq -e '.metadata.vulnerabilities.high > 0' audit-report.json >/dev/null; then echo '⚠️ High-severity vulnerabilities detected' >&2; exit 2; fi",
            "timeout": 90
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ Path is quoted and uses official variable
- ✅ Output saved securely (no sensitive data exposure)
- ✅ Validates JSON safely with `jq`
- ✅ Reasonable timeout: 90 seconds for network operation
- ✅ Clear error message without sensitive details

---

### Example 3: Prevent Sensitive Data in Prompts (UserPromptSubmit)

**Purpose:** Block prompts containing API keys or passwords

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": ".*",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Analyze the user prompt for sensitive data: API keys (patterns like 'sk-', 'api_key='), passwords ('password=', 'pwd='), or secrets. If found, return decision: block with reason explaining what sensitive data pattern was detected (but DON'T include the actual sensitive data in the response). Otherwise return decision: approve. Prompt: $ARGUMENTS",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ Uses LLM evaluation (type: prompt)
- ✅ Doesn't log sensitive data (instructs LLM not to include it)
- ✅ Short timeout for quick validation
- ✅ Blocks proactively, preventing leakage

---

### Example 4: Enforce Git Branch Policy (PreToolUse)

**Purpose:** Prevent commits directly to main branch

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash.*git commit",
        "hooks": [
          {
            "type": "command",
            "command": "BRANCH=$(git branch --show-current); if [ \"$BRANCH\" == \"main\" ] || [ \"$BRANCH\" == \"master\" ]; then echo \"❌ Direct commits to $BRANCH are not allowed. Create a feature branch.\" >&2; exit 2; fi; echo \"✅ Committing to $BRANCH\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ No user input processed (reads from git)
- ✅ Fast operation (< 1 second typical)
- ✅ Clear error guidance
- ✅ Non-destructive validation

---

### Example 5: Require Tests to Pass Before Code Review (SubagentStop)

**Purpose:** Ensure code-reviewer subagent runs tests first

```json
{
  "hooks": {
    "SubagentStop": [
      {
        "matcher": "code-reviewer",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if code-reviewer mentioned running tests and if tests passed. If tests weren't run or failed, return decision: block with reason 'Please run the test suite before completing review'. Otherwise return decision: approve. Subagent response: $ARGUMENTS",
            "timeout": 20
          }
        ]
      }
    ]
  }
}
```

**Security analysis:**
- ✅ Uses LLM for semantic validation
- ✅ Doesn't execute commands (read-only validation)
- ✅ Timeout appropriate for LLM call
- ✅ No sensitive data exposure risk

---

## Hook Execution Details

### Parallelization

**All matching hooks for an event run simultaneously.**

**Example:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {"type": "command", "command": "eslint \"$FILE_PATH\"", "timeout": 30},
          {"type": "command", "command": "prettier --check \"$FILE_PATH\"", "timeout": 20}
        ]
      }
    ]
  }
}
```

**Both `eslint` and `prettier` run at the same time.**

**Result aggregation:**
- If ANY hook exits with code 2 → operation is blocked
- If ALL hooks exit with code 0 → operation proceeds
- Logs from all hooks are collected and shown

### Deduplication

**Identical commands execute only once per event.**

**Example:**
```json
{
  "hooks": {
    "PreToolUse": [
      {"matcher": "Edit.*\\.js", "hooks": [{"command": "npm test", "timeout": 60}]},
      {"matcher": "Write.*\\.js", "hooks": [{"command": "npm test", "timeout": 60}]}
    ]
  }
}
```

**If both matchers match the same event, `npm test` runs only once.**

### Environment and Variables

**Hooks execute with:**
- **Working directory:** `$CLAUDE_PROJECT_DIR` or current `cwd`
- **PATH:** Inherits from Claude Code process
- **Environment variables:** Inherits from Claude Code
- **Special variables:**
  - `$CLAUDE_PROJECT_DIR`: Project root
  - `$CLAUDE_CODE_REMOTE`: Execution context (local/remote)
  - Event-specific variables (see Input/Output Protocol)

**Example:**
```bash
# Access project files
cat "$CLAUDE_PROJECT_DIR/package.json"

# Check execution context
if [ "$CLAUDE_CODE_REMOTE" == "true" ]; then
    echo "Running in remote environment"
fi
```

---

## Debugging Hooks

### Enable Debug Logging

```bash
claude --debug
```

**Output shows:**
- Which hooks matched each event
- Commands executed
- Exit codes and output
- Execution time
- Blocking decisions

### Check Hook Registration

Within Claude Code session:

```
/hooks
```

**Shows:**
- All registered hooks
- Event types
- Matchers
- Status (enabled/disabled)

### Common Debugging Issues

#### Hook Doesn't Fire

**Symptoms:** Hook never executes

**Diagnosis:**
1. Check matcher pattern: Does it match the tool/event?
2. Verify JSON syntax: Use `jq` to validate settings.json
3. Check file location: Is settings file in correct directory?
4. Restart Claude Code: Changes require session restart

**Fix:**
```bash
# Test matcher locally
echo "Edit" | grep -E "^Edit$"  # Should match

# Validate JSON
jq . .claude/settings.json

# Check hooks list
# In Claude session: /hooks
```

#### Hook Fires But Fails

**Symptoms:** Hook executes but exits with error

**Diagnosis:**
1. Check command syntax: Test command in terminal
2. Verify variables: Are event variables available?
3. Check timeout: Is hook taking too long?
4. Review logs: Check --debug output

**Fix:**
```bash
# Test command independently
FILE_PATH="/path/to/file.js" bash -c 'eslint "$FILE_PATH"'

# Check timeout is sufficient
time bash -c '[your hook command]'
```

#### Hook Blocks Unexpectedly

**Symptoms:** Operations blocked when they shouldn't be

**Diagnosis:**
1. Check exit codes: Is hook exiting with 2 unintentionally?
2. Review logic: Is validation too strict?
3. Check stderr: What error message is shown?

**Fix:**
```bash
# Add explicit logging
echo "Validation starting..." >&2
[validation logic]
echo "Validation passed" >&2
exit 0
```

---

## Best Practices Summary

### Security (HIGHEST PRIORITY)
1. ✅ **Always quote variables**: `"$VAR"` not `$VAR`
2. ✅ **Validate paths**: Prevent path traversal
3. ✅ **Avoid credentials**: Never access `.env`, `.git/`, secrets
4. ✅ **Fail safely**: Don't expose sensitive data in errors
5. ✅ **Peer review**: Get security-aware review before deployment
6. ✅ **Test in isolation**: Test hooks in safe environment first

### Performance
1. ✅ **Keep hooks fast**: < 30s for validation, < 2min for tests
2. ✅ **Set timeouts**: Always configure reasonable timeouts
3. ✅ **Avoid network when possible**: Network calls are slow and can fail
4. ✅ **Cache when appropriate**: Don't re-validate unchanged files

### Reliability
1. ✅ **Handle errors gracefully**: Don't break workflows on hook failure
2. ✅ **Provide clear messages**: Users should understand why operations blocked
3. ✅ **Test edge cases**: Empty inputs, special characters, long strings
4. ✅ **Use specific matchers**: Avoid overly broad `".*"` patterns

### Maintainability
1. ✅ **Document hooks**: Explain purpose and security review in comments
2. ✅ **Version control**: Commit `.claude/settings.json` for project hooks
3. ✅ **Use .local for personal**: Keep personal hooks in `settings.local.json`
4. ✅ **Regular audits**: Review hooks quarterly for security and relevance

---
